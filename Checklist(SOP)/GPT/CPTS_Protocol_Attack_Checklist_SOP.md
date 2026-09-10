# CPTS — Protocol-Based Attack Checklist & SOP

> Objective: map **open protocol → condition → attack → outcome**. Start with `nmap`, identify the service, test anonymous/default/weak configurations, then pivot using any credential or access obtained. Scope is limited to the main CPTS notes; `Extras/` is excluded.

---

# 1. Universal protocol triage

- [ ] Run `nmap` across all relevant ports, not only the common top ports when time permits.
- [ ] Record service name, version, TLS, authentication mode, default paths and unusual high ports.
- [ ] Check for anonymous/null/default access before password attacks.
- [ ] Check for weak/legacy protocol behavior.
- [ ] Gather banners/configuration information before exploitation.

### Golden rule

**Open port alone does not equal exploit.** The next question is always: **what condition makes the service attackable?**

---

# 2. DNS

### Check
- [ ] Authoritative nameservers.
- [ ] Interesting A/AAAA/CNAME/MX/TXT records.
- [ ] Hidden hostnames/subdomains.
- [ ] Whether AXFR/zone transfer is permitted.
- [ ] Whether DNS names expose internal infrastructure.

### Condition → attack
- **Zone transfer permitted** → retrieve full zone data → host/record inventory.
- **Discoverable subdomains/vhosts** → expand attack surface.
- **Internal names leaked in DNS** → target internal services/hosts.

### Tools / outcome
- `dig`
- `dnsenum`
- `fierce`
- `subfinder`
- Outcome: hostnames, IPs, services and trust relationships.

---

# 3. SMB / NetBIOS / RPC

### Check
- [ ] Null/anonymous sessions.
- [ ] Guest access.
- [ ] Share list and permissions.
- [ ] User/group enumeration.
- [ ] SMB signing/protocol configuration where relevant to relay decisions.
- [ ] Credential reuse and local admin access.

### Condition → attack
- **Null/guest enumeration** → users/shares/domain info.
- **Readable sensitive share** → credential/config discovery.
- **Writable share** → file placement/pivot opportunity where the application/process consumes it.
- **Captured NTLM + relayable target** → `ntlmrelayx` relay.
- **Valid admin credential/hash** → SMB/WMI/SMB-exec lateral movement.

### Tools / outcomes
- `NetExec`
- `enum4linux-ng`
- `smbmap`
- `smbclient`
- `rpcclient`
- Impacket SMB/RPC family (`psexec`, `wmiexec`, `smbexec`, `smbclient`, `ntlmrelayx`)
- Outcome: files, credentials, remote execution, relay, lateral movement.

---

# 4. Kerberos

### Check
- [ ] Domain user context.
- [ ] SPN-backed service accounts.
- [ ] Users with pre-auth disabled.
- [ ] Trust relationships.

### Condition → attack
- **Valid domain user + SPN account** → Kerberoasting.
- **Pre-auth disabled** → AS-REP roasting.
- **Cross-domain trust + SPNs** → cross-forest Kerberoast path.
- **KRBTGT material + domain/SID prerequisites** → golden-ticket path.

### Tools / outcomes
- `Kerbrute`
- `Rubeus`
- `impacket-getuserspns`
- `mimikatz`
- `impacket-ticketer`
- Outcome: cracked credentials, tickets, domain access.

---

# 5. LDAP

### Check
- [ ] Anonymous bind or weakly authenticated bind.
- [ ] Discoverable naming context/domain.
- [ ] Users/groups/computers and trust objects.
- [ ] ACLs/attributes relevant to the current principal.

### Condition → attack
- **Anonymous/weak bind** → directory enumeration.
- **Writable object/attribute** → AD ACL abuse path.
- **Web application puts user input into LDAP filters** → LDAP injection (web branch).

### Tools / outcomes
- `ldapsearch`
- `windapsearch`
- `bloodyAD`
- `PowerView` (Windows-side transfer where used)
- Outcome: AD object inventory and ACL attack paths.

---

# 6. FTP

### Check
- [ ] Anonymous login.
- [ ] Writable directories.
- [ ] Readable sensitive files.
- [ ] TLS/authentication behavior.
- [ ] Weak credentials where rate limits permit.

### Condition → attack
- **Anonymous read** → file/config discovery.
- **Anonymous/write-enabled** → upload/replace files; possible secondary code execution if another service consumes them.
- **Valid credentials** → file access and credential reuse.

### Tools / outcomes
- `nmap`
- `telnet`
- `openssl s_client` for TLS service inspection
- `Hydra` / `Medusa` when appropriate
- Outcome: file access/credential discovery.

---

# 7. SSH

### Check
- [ ] Password authentication enabled.
- [ ] Key-based authentication and exposed private keys.
- [ ] Weak/default credentials.
- [ ] Version/configuration weaknesses.

### Condition → attack
- **Recovered private key** → SSH access.
- **Valid password** → SSH access.
- **Weak credential policy** → password testing.

### Tools / outcomes
- `nmap`
- SSH client/native tools
- `Hydra` / `Medusa` when supported by the notes
- Outcome: interactive shell / pivot.

---

# 8. RDP

### Check
- [ ] Is RDP exposed?
- [ ] Security level / authentication configuration.
- [ ] Which users/groups may RDP.
- [ ] Valid credentials or NTLM material.

### Condition → attack
- **Valid account permitted RDP** → interactive Windows access.
- **CanRDP relationship** → RDP lateral movement.
- **Weak/legacy RDP security condition** → protocol-level attack path where the notes support it.

### Tools / outcomes
- `xfreerdp`
- `rdesktop`
- `Remmina`
- `rdp-sec-check`
- `NetExec`
- Outcome: Windows desktop/session.

---

# 9. WinRM / WMI / Task Scheduler / SMB execution

### Check
- [ ] WinRM exposed.
- [ ] Current user is allowed remote management.
- [ ] WMI/SMB task/service execution possible with current credentials.

### Condition → attack
- **CanPSRemote + credentials** → WinRM shell.
- **Valid admin + WMI** → `wmiexec`.
- **Valid admin + Task Scheduler** → `atexec`.
- **Valid admin + SMB service execution** → `psexec` / `smbexec`.

### Tools / outcomes
- `Evil-WinRM`
- `impacket-wmiexec`
- `impacket-atexec`
- `impacket-psexec`
- `impacket-smbexec`
- Outcome: remote command execution.

---

# 10. SMTP

### Check
- [ ] Banner/version.
- [ ] VRFY/EXPN/RCPT behavior for user enumeration.
- [ ] Authentication modes.
- [ ] Relay behavior only when appropriate to the lab condition.

### Condition → attack
- **User enumeration permitted** → build valid username list.
- **Weak service credentials** → authenticated SMTP access.
- **Relay misconfiguration** → mail relay abuse where applicable.

### Tools / outcomes
- `smtp-user-enum`
- `nmap`
- `telnet` / `openssl s_client`
- Outcome: usernames/authentication foothold.

---

# 11. SNMP

### Check
- [ ] UDP/161 exposed.
- [ ] Community string defaults/weakness.
- [ ] Readable OIDs/configuration data.
- [ ] Write access if the service permits it.

### Condition → attack
- **Default/guessable community string** → SNMP enumeration.
- **Sensitive OIDs** → usernames, processes, interfaces, routes, config information.
- **Write-enabled community** → configuration-changing impact where supported.

### Tools / outcomes
- `onesixtyone`
- `snmpwalk`
- `braa`
- Outcome: infrastructure information / credentials/configuration clues.

---

# 12. NFS

### Check
- [ ] Export list.
- [ ] Host restrictions.
- [ ] Root squashing / UID mapping.
- [ ] Write permissions.

### Condition → attack
- **Accessible export** → mount filesystem.
- **Writable export + unsafe root mapping** → privileged file manipulation / LPE.

### Tools / outcomes
- `showmount`
- `mount -t nfs`
- Outcome: file access or privilege-escalation path.

---

# 13. MySQL

### Check
- [ ] Authentication exposed to network.
- [ ] Default/weak credentials.
- [ ] User privileges.
- [ ] Dangerous file read/write functionality if the account permits it.

### Condition → attack
- **Valid DB credentials** → database enumeration.
- **High DB privileges / file-write ability** → application/system impact as permitted by configuration.
- **Credential reuse** → attack other hosts/services.

### Tool
- `mysql client`

---

# 14. MSSQL

### Check
- [ ] Discover MSSQL instance/version.
- [ ] Test current credentials.
- [ ] Determine sysadmin/SQLAdmin-level control.
- [ ] Identify linked servers or command-execution functionality represented in the notes.

### Condition → attack
- **Valid MSSQL credentials** → DB enumeration.
- **SQLAdmin/sysadmin privileges** → command execution/lateral movement path.
- **Linked-server trust/control** → pivot into another SQL host.

### Tools / outcomes
- `impacket-mssqlclient`
- `sqsh`
- `PowerUpSQL`
- `nmap`
- Outcome: DB access, code execution or pivot.

---

# 15. Oracle / TNS

### Check
- [ ] Oracle listener/TNS exposure.
- [ ] SID/service names.
- [ ] Default/weak credentials.
- [ ] File/database privilege conditions.

### Condition → attack
- **Weak/default Oracle credentials** → authenticated DB access.
- **Vulnerable privilege/file condition** → OS or filesystem impact where supported.

### Tools / outcomes
- `ODAT`
- `sqlplus`
- `nmap`
- Outcome: Oracle access/credential discovery/host impact depending on privilege.

---

# 16. IPMI

### Check
- [ ] UDP/623 exposure.
- [ ] Authentication/configuration mode.
- [ ] Default credentials or exposed authentication material.

### Condition → attack
- **Weak/default IPMI authentication** → management access.
- **Captured/extracted IPMI hash material** → offline cracking → valid management credential.

### Tools / outcomes
- `nmap`
- `Metasploit` IPMI modules represented in the notes
- `hashcat` for recovered hash material
- Outcome: out-of-band management access.

---

# 17. R-services / legacy remote services

### Check
- [ ] `rsh` / `rlogin` / related trust-based authentication.
- [ ] Host-based trust configuration.
- [ ] Weak/legacy authentication.

### Condition → attack
- **Trusted-host configuration / weak auth** → remote shell.

### Outcome
- Remote shell and subsequent LPE.

---

# 18. O365 / cloud-authentication surface represented in notes

### Check
- [ ] Build likely usernames.
- [ ] Test whether accounts are valid.
- [ ] Determine whether password-spray conditions and throttling allow testing.

### Condition → attack
- **Valid account + spray-safe conditions** → O365 password spray.
- **Recovered cloud credential reused internally** → pivot back to enterprise hosts/services.

### Tool
- `o365spray`
- `username-anarchy`

---

# Protocol SOP — Low access → high access

## Phase 1 — Enumerate everything

1. [ ] `nmap` all relevant hosts.
2. [ ] Group ports by protocol/service.
3. [ ] Record versions and authentication modes.

## Phase 2 — Test “cheap wins” first

1. [ ] Anonymous/null/guest access.
2. [ ] Default/weak credentials.
3. [ ] Readable/writable shares/exports.
4. [ ] Version-specific vulnerabilities.
5. [ ] User enumeration.

## Phase 3 — Convert information into credentials

1. [ ] Search SMB/NFS/FTP/DB/web content for passwords/keys/configs.
2. [ ] Crack hashes with `hashcat` / `John`.
3. [ ] Reuse credentials across SMB/RDP/WinRM/SSH/DB/O365.

## Phase 4 — Convert credentials into remote execution

1. [ ] SMB → `psexec` / `wmiexec` / `smbexec`.
2. [ ] WinRM → `Evil-WinRM`.
3. [ ] RDP → `xfreerdp` / `rdesktop` / `Remmina`.
4. [ ] SSH / FTP / database shells → local host access.

## Phase 5 — Privilege escalation

1. [ ] Apply the **Windows LPE** or **Linux LPE** checklist immediately.
2. [ ] Re-run service/protocol enumeration with stronger privileges.
3. [ ] Return to AD when the host is domain joined.

---

# Burnout / panic decision tree

```text
START
  |
  v
nmap -> What protocol is open?
  |
  +--> SMB/RPC --> null/guest? shares? creds? relay? -> remote exec
  |
  +--> Kerberos --> SPN? pre-auth? trust? -> roast/ticket path
  |
  +--> LDAP --> anonymous bind? writable object? -> AD/ACL path
  |
  +--> DNS --> AXFR? names/subdomains? -> expand targets
  |
  +--> RDP --> valid RDP user? security check? -> desktop
  |
  +--> WinRM/WMI --> credentials? -> remote shell
  |
  +--> FTP --> anonymous/writable? -> files/credentials
  |
  +--> SSH --> key/credential? -> shell
  |
  +--> SMTP --> user enum/auth? -> usernames/credential path
  |
  +--> SNMP --> community? -> infrastructure secrets
  |
  +--> NFS --> writable export? -> file access/LPE
  |
  +--> MySQL/MSSQL/Oracle --> creds/privileges? -> DB/host impact
  |
  +--> IPMI --> auth weakness/hash? -> management access
  |
  v
Whatever access you gain: re-enumerate from the new privilege level.
```

## “Completely stuck” fallback order

1. [ ] `nmap`
2. [ ] Identify anonymous/default access.
3. [ ] SMB shares / FTP / NFS readable data.
4. [ ] DNS/subdomain/user enumeration.
5. [ ] Kerberos roasting conditions.
6. [ ] Valid credentials → RDP/WinRM/SSH/SMB.
7. [ ] Database privilege checks.
8. [ ] SNMP/IPMI secrets.
9. [ ] Re-enter Windows/Linux/AD LPE checklists.

## Source

[TamHoTsun-Alvin/CPTS_Notes](https://github.com/TamHoTsun-Alvin/CPTS_Notes) — especially `Service Enumeration and Attack/`, `ActiveDirectory/`, `WebAttacks/`, `Privilege Escalation/`, and `FileTransfer/`.
