# CPTS — Active Directory Attack Checklist & SOP

> Exam-use checklist. The goal is to identify **conditions first**, then choose the shortest attack path. Scope is limited to techniques represented in the main CPTS notes; `Extras/` is excluded.

---

## 0. Panic reset — first 2 minutes

- [ ] Write down: **IP(s), hostname(s), domain name, username(s), password/hash(es), current privilege, reachable services**.
- [ ] Ask: **Do I have only network access, a normal domain user, local admin, or domain-level privileged access?**
- [ ] Identify the likely DC from **DNS / Kerberos / LDAP / SMB**.
- [ ] Do one broad recon pass before chasing exploits.
- [ ] Do not repeat the same failed attack more than once without changing the condition being tested.

### Minimum tools
- `nmap`
- `NetExec (nxc)`
- `Kerbrute`
- `enum4linux-ng`
- `ldapsearch` / `windapsearch`
- `bloodhound-python` / `SharpHound` + `BloodHound`

---

## 1. Establish the AD boundary

### Check
- [ ] DNS identifies the domain/DC.
- [ ] TCP/UDP services suggest **53 DNS, 88 Kerberos, 389/636 LDAP, 445 SMB, 3389 RDP, 5985/5986 WinRM**.
- [ ] SMB banner/domain naming confirms Windows domain membership.
- [ ] Trusts or multiple domain names appear in DNS/LDAP/AD output.

### Condition → attack path
- **DC identified** → focus enumeration on the domain instead of random host scanning.
- **Multiple domains/trusts identified** → enumerate trust direction/transitivity before attacking across domains.
- **Multiple hosts share the same domain** → plan lateral movement and credential reuse checks.

### Tools / outcomes
- `nmap` → service/version map.
- `NetExec` → SMB/LDAP/WinRM reachability and authentication results.
- `dig`, `dnsenum`, `fierce` → DNS/domain clues.
- `tcpdump`, `Wireshark` → protocol and host-discovery clues.

---

## 2. Username and object enumeration

- [ ] Generate likely usernames with `username-anarchy` when only names are known.
- [ ] Test username validity with `Kerbrute`.
- [ ] Enumerate users/groups/SIDs with `NetExec`, `enum4linux-ng`, `rpcclient`, `windapsearch`, `ldapsearch`.
- [ ] Identify service accounts and machine accounts.
- [ ] Record privileged groups and obvious admin accounts.

### Condition → attack path
- **Valid username list** → password spraying becomes viable.
- **Service account with SPN** → Kerberoasting.
- **User with pre-auth disabled** → AS-REP roasting.
- **Membership in privileged/built-in group** → possible RDP/WinRM/admin access.

---

## 3. Password policy before spraying

- [ ] Get the domain password policy with `NetExec` / `enum4linux-ng` / `PowerView` (when available).
- [ ] Record minimum length, lockout threshold and lockout observation window.
- [ ] Prefer candidate passwords based on organization context and material already found in shares/files.

### Condition → attack path
- **Spray-safe policy + candidate password** → password spray.
- **Low lockout threshold / risky policy** → slow/limited testing or stop spraying.

### Tools / outcomes
- `NetExec` → authentication testing and policy checks.
- `Kerbrute` → username/password testing.
- `DomainPasswordSpray.ps1` → Windows-side spray.
- `o365spray` → cloud/O365 credential testing when relevant.

---

## 4. LLMNR / NBT-NS / poisoning branch

### Check
- [ ] You have network visibility to the broadcast domain.
- [ ] Windows hosts are generating LLMNR/NBT-NS requests.
- [ ] Name resolution traffic can be observed.

### Condition → attack
- **Network access + LLMNR/NBT-NS activity** → `Responder` poisoning.
- **Captured NTLMv2 challenge/response** → offline password cracking.
- **Cracked reusable password** → test the account across SMB/WinRM/RDP and re-enumerate AD.

### Tools / outcomes
- `Responder` → NTLMv2 capture.
- `hashcat` / `John` → plaintext password candidate.
- `NetExec` → validate credential scope and lateral-movement options.

---

## 5. Kerberoasting / AS-REP roasting branch

### Check
- [ ] You have a valid AD account (for normal Kerberoasting).
- [ ] User/service accounts expose SPNs.
- [ ] Some accounts permit AS-REP without pre-auth.

### Condition → attack
- **Valid domain user + SPN-backed account** → Kerberoasting.
- **User with pre-auth disabled** → AS-REP roasting.
- **Obtained roastable material + weak service-account password** → crack offline.
- **Cracked service account is privileged/reused** → authenticate laterally and re-enumerate.

### Tools / outcomes
- `impacket-getuserspns` → SPN/TGS material.
- `Rubeus` → Kerberos roast/ticket workflows.
- `PowerView` → SPN enumeration/ticket requests.
- `hashcat` / `John` → plaintext credential.

---

## 6. SMB / share pillaging branch

### Check
- [ ] Enumerate SMB shares.
- [ ] Test read/write permissions.
- [ ] Search filenames and content for credentials, scripts, backups, deployment files, configs and keys.
- [ ] Check whether discovered credentials work on other hosts.

### Condition → attack
- **Readable share containing credentials** → credential reuse.
- **Writable share used by a privileged process/user** → file-placement abuse may become relevant.
- **Local admin credential/hash found** → pass-the-hash / lateral movement.

### Tools / outcomes
- `NetExec --shares / spider_plus` → share and file discovery.
- `smbmap`, `smbclient`, `rpcclient` → detailed SMB access.
- `Snaffler`, `PowerHuntShares` → automated sensitive-file discovery.
- `impacket-psexec`, `wmiexec`, `atexec`, `smbexec` → remote execution after valid access.

---

## 7. BloodHound / ACL branch

### Check
- [ ] Collect domain relationships.
- [ ] Identify your current principal and its outbound control edges.
- [ ] Look for paths to high-value users/groups/computers.
- [ ] Prioritize direct, short attack paths before complex chains.

### Condition → attack
- **ForceChangePassword on target** → reset target password → authenticate as target.
- **GenericWrite on user** → modify permitted attributes / service-principal-related paths → targeted Kerberoast or other note-supported abuse.
- **GenericAll on user/group** → broad control of the object → password/group-control path.
- **AddSelf on group** → add current principal to the group → inherit group privileges.
- **Control of privileged group** → group membership change → higher AD privileges.
- **LAPS-readable relationship** → retrieve local-admin credential where the account is permitted to read it.

### Tools / outcomes
- `BloodHound` + `SharpHound` / `bloodhound-python` → attack path graph.
- `PowerView` → ACL validation and AD queries.
- `bloodyAD` → object manipulation / ACL abuse.
- Native `setspn` / PowerView SPN functions → SPN modifications when the ACL permits.

---

## 8. RDP / WinRM access branch

### Check
- [ ] Determine who can RDP or use PowerShell Remoting.
- [ ] Validate current credentials on candidate machines.

### Condition → attack
- **CanRDP / RDP group membership + valid credentials** → RDP session.
- **CanPSRemote / Remote Management Users + valid credentials** → WinRM shell.
- **Remote access achieved** → local privilege escalation + credential pillage + second AD enumeration pass.

### Tools / outcomes
- `xfreerdp`, `rdesktop`, `Remmina` → interactive RDP.
- `Evil-WinRM` → WinRM shell.
- `NetExec` → authentication and target selection.

---

## 9. Lateral movement / credential reuse

- [ ] Test recovered passwords against other hosts.
- [ ] Test NTLM hashes where supported instead of wasting time cracking immediately.
- [ ] Enumerate sessions and admins on reachable hosts.
- [ ] Re-run BloodHound after gaining a materially stronger account/host position.

### Condition → attack
- **Valid local/domain account + reachable SMB/WMI/WinRM** → remote execution.
- **NTLM hash + SMB/WMI path** → pass-the-hash.
- **Admin credential on multiple machines** → rapid lateral movement.

### Tools / outcomes
- `NetExec` → validate breadth of access.
- `Invoke-TheHash` / Impacket remote-exec family → remote execution using hash material.

---

## 10. DCSync branch — only when the prerequisite exists

### Check
- [ ] Determine whether the current account has **Replicating Directory Changes** and **Replicating Directory Changes All** rights (or equivalent privileged control described in the notes).
- [ ] Do not attempt DCSync merely because the domain is visible.

### Condition → attack
- **Required replication rights** → DCSync.
- **DCSync success** → domain NTLM hashes / secret material.
- **KRBTGT material obtained** → golden-ticket branch.

### Tools / outcomes
- `PowerView` / `Get-ObjectAcl` → verify rights.
- `impacket-secretsdump` / `mimikatz` → credential/replication extraction.
- Outcome: domain-wide credential material suitable for further escalation.

---

## 11. Cross-domain / trust branch

### Check
- [ ] Enumerate one-way vs bidirectional trust.
- [ ] Determine transitive/non-transitive relationships.
- [ ] Identify child/parent domain relationship.
- [ ] In the child-to-parent path, confirm prerequisites from the notes: child compromise, child SID, child KRBTGT hash, target name/FQDN, parent privileged SID, and lack of effective SID filtering.

### Condition → attack
- **Cross-forest/other-domain SPNs + valid authentication context** → cross-forest Kerberoast.
- **Child compromise + required trust conditions** → ExtraSIDs / child-to-parent trust escalation.

### Tools / outcomes
- `PowerView` → trust/SPN enumeration.
- `Rubeus` → Kerberos ticket operations.
- `impacket-raisechild` / ticketing tooling → trust-escalation workflow.
- Outcome: access in the higher-value domain when all trust prerequisites align.

---

## 12. MSSQL / SQLAdmin branch inside AD

### Check
- [ ] Discover MSSQL instances.
- [ ] Determine whether current credentials authenticate.
- [ ] Identify SQLAdmin/sysadmin-level privileges and linked-server opportunities referenced by the notes.

### Condition → attack
- **MSSQL reachable + valid credentials** → database enumeration.
- **SQLAdmin/sysadmin-level control** → SQL-level command execution/lateral movement path.
- **SQL credential reused in AD/hosts** → broader credential attack.

### Tools / outcomes
- `PowerUpSQL`
- `impacket-mssqlclient`
- `sqsh`
- Outcome: database access, execution path, or credentials for further movement.

---

# AD SOP — Low access → High access

## Phase 1 — Network only

1. [ ] `nmap` all relevant hosts/ports.
2. [ ] Identify the DC and domain.
3. [ ] Run DNS/domain enumeration (`dig`, `dnsenum`, `fierce`).
4. [ ] Check for LLMNR/NBT-NS opportunities with `Responder` when network access permits.
5. [ ] Build an initial username list.

## Phase 2 — Normal domain user

1. [ ] Get password policy.
2. [ ] Enumerate users/groups/computers/shares.
3. [ ] Test for **Kerberoast** and **AS-REP roast** conditions.
4. [ ] Run BloodHound collection and inspect the shortest privilege paths.
5. [ ] Enumerate SMB shares and sensitive files.
6. [ ] Test credentials against RDP/WinRM/SMB.

## Phase 3 — First useful foothold

1. [ ] Pick the shortest path: cracked service account, share credential, RDP, WinRM, or ACL abuse.
2. [ ] Move to the best host.
3. [ ] Perform Windows local privilege enumeration there.
4. [ ] Pillage credentials/files.
5. [ ] Re-run AD enumeration with the stronger account/context.

## Phase 4 — Domain privilege

1. [ ] Re-check BloodHound for direct ACL/group paths.
2. [ ] If replication rights exist, perform DCSync.
3. [ ] If child/parent trust conditions exist, evaluate ExtraSIDs path.
4. [ ] If KRBTGT/domain secret material is obtained, evaluate the golden-ticket path represented in the notes.
5. [ ] Validate the final privileged access and preserve the exact proof required by the exam.

---

# Burnout / panic decision tree

```text
START
  |
  v
Do I know my current identity?
  |-- NO --> Identify user/group/host first.
  |
  v
Do I know the domain/DC and open services?
  |-- NO --> nmap + DNS + SMB/Kerberos/LDAP identification.
  |
  v
Do I have credentials?
  |-- NO --> Network poisoning -> username enumeration -> credential capture paths.
  |
  |-- YES --> Get password policy -> spray carefully.
  |
  v
Do I have a normal domain account?
  |-- YES --> Kerberoast / AS-REP roast -> BloodHound -> shares.
  |
  v
Do I have a reachable Windows host?
  |-- YES --> RDP/WinRM/SMB -> Windows LPE -> pillage.
  |
  v
Did BloodHound show a direct control edge?
  |-- YES --> ForceChangePassword / GenericWrite / GenericAll / AddSelf / LAPS path.
  |
  v
Do I have replication rights?
  |-- YES --> DCSync.
  |
  v
Do I have child-domain + trust prerequisites?
  |-- YES --> trust / ExtraSIDs path.
  |
  v
STOP chasing exotic paths. Re-enumerate with the strongest access you have.
```

## “I am completely stuck” fallback order

1. [ ] Reconfirm **identity + domain + services**.
2. [ ] Check **password policy**.
3. [ ] Check **Kerberoasting / AS-REP roasting**.
4. [ ] Check **shares and credentials**.
5. [ ] Run **BloodHound** and look for one-hop control.
6. [ ] Check **RDP / WinRM** reachability.
7. [ ] Run **Windows LPE enumeration** on the strongest host.
8. [ ] Re-enter AD enumeration from the new privilege level.

## Source

[TamHoTsun-Alvin/CPTS_Notes](https://github.com/TamHoTsun-Alvin/CPTS_Notes) — especially `ActiveDirectory/` and related `CommonPayload/`, `Service Enumeration and Attack/`, and `Privilege Escalation/` notes.
