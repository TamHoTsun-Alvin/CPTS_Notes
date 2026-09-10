### Protocol-based Attack Checklist

- SMB (445/139) → null session / anonymous, share enum (smbmap / enum4linux / nxc), writable shares, PTH, relay (if signing disabled).
- LDAP (389/636) → anonymous bind, password policy, user enum.
- Kerberos (88) → userenum, ASREPRoast, Kerberoast, spraying.
- WinRM (5985/5986) → evil-winrm with creds/hash.
- RDP (3389) → xfreerdp with creds/hash (Restricted Admin).
- MSSQL (1433) → PowerUpSQL / impacket, xp_cmdshell if privileged.
- MySQL / Oracle TNS / NFS / FTP / SSH / SNMP / SMTP / IPMI / R-services / rsync → default/weak creds, anonymous, known CVEs, file access.
- LLMNR/NBT-NS → Responder poisoning.
- DNS → zone transfer / enum if misconfigured.

**Protocol SOP**

1. Port scan (nmap) → identify open services.
2. Null/anonymous enum first (SMB, LDAP, NFS, FTP, SNMP).
3. Credential attacks (spray, default, captured hashes).
4. Protocol-specific abuse (Kerberoast, PTH over SMB/WinRM/RDP, SQL xp_cmdshell, etc.).
5. Use obtained access for further AD / privesc / pivot.