### AD Attack Checklist

- Network access only → Start Responder (LLMNR/NBT-NS poisoning) → capture NTLMv2 hashes → crack → cleartext creds.
- No credentials + DC reachable → kerbrute userenum → valid usernames; enum4linux-ng / ldapsearch / Windapsearch → password policy, users, groups, anonymous bind.
- Valid domain user → Kerberoasting (impacket-GetUserSPNs / Rubeus / PowerView) → crack TGS → service account plaintext; ASREPRoasting (accounts with DONT_REQ_PREAUTH).
- Valid domain user → Password spraying (nxc / kerbrute / DomainPasswordSpray) using policy-aware passwords or password reuse across accounts.
- Domain user with CanRDP / CanPSRemote / SQLAdmin (BloodHound / PowerView) → RDP / WinRM / MSSQL access to other hosts.
- Any domain credentials → BloodHound collection (SharpHound / bloodhound-python) → path finding.
- User with ForceChangePassword / GenericAll / GenericWrite / AddSelf / WriteDACL (PowerView ACL enum) → reset passwords, add SPN (targeted Kerberoast), add to groups, grant DCSync rights.
- User/group with Replicating Directory Changes + All → DCSync (secretsdump / mimikatz) → full NTDS.dit hashes.
- Local admin / Domain Admin hash → Pass-the-Hash (impacket-psexec/wmiexec, nxc, evil-winrm, xfreerdp, Invoke-TheHash, mimikatz) → lateral movement.
- High-priv ticket or hash → Pass-the-Ticket / silver/golden ticket (Rubeus / mimikatz) if further abuse needed.
- Cross-forest / trust relationships → Trust enumeration (PowerView) → possible cross-forest attacks.

**AD SOP (low → high)**

1. Network access → Responder + kerbrute + enum4linux-ng / ldapsearch / nxc null sessions.
2. Obtain first valid domain user (spray / cracked Responder hash / ASREP).
3. Collect BloodHound data + PowerView / SharpView enumeration (SPNs, ACLs, privileges, trusts).
4. Kerberoast / ASREPRoast → crack → better accounts.
5. Abuse interesting ACLs / privileges (ForceChangePassword, GenericAll, etc.) or CanRDP/CanPSRemote.
6. Pivot / lateral with PTH / WinRM / RDP.
7. Reach Domain Admin / Enterprise Admin or DCSync-capable principal → DCSync → full domain compromise.
8. If trusts exist, expand to other forests.