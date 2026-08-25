### Windows Privilege Escalation Checklist

- Low-priv shell → whoami /priv, whoami /groups, systeminfo, Get-Hotfix, net localgroup, net user, tasklist /svc, netstat -ano, ipconfig /all, arp -a, route print.
- Use the following to check for installed application:
```
PS C:\htb> $INSTALLED = Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |  Select-Object DisplayName, DisplayVersion, InstallLocation
PS C:\htb> $INSTALLED += Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, DisplayVersion, InstallLocation
PS C:\htb> $INSTALLED | ?{ $_.DisplayName -ne $null } | sort-object -Property DisplayName -Unique | Format-Table -AutoSize
```
- AlwaysInstallElevated = 1 → msfvenom .msi → SYSTEM.
- Unquoted service path / weak service permissions / writable service binary → replace binary or reconfigure → SYSTEM.
- Weak file/folder permissions on service binaries, scheduled tasks, or autorun locations → overwrite → SYSTEM.
- DLL hijacking opportunities (missing DLLs in writable PATH) → plant malicious DLL.
- Stored credentials / autologon / Credential Manager / DPAPI / registry / web.config / scripts → extract and reuse.
- SeImpersonatePrivilege / SeAssignPrimaryTokenPrivilege (or JuicyPotato-style) → potato-family escalation (if tools present).
- Vulnerable kernel / missing patches (systeminfo + public exploits) → kernel exploit.
- Named pipe impersonation opportunities.
- High-priv group membership (Administrators, Backup Operators, etc.) or token privileges.
- Run WinPEAS for automated coverage of the above.

**Windows Privesc SOP**

1. Basic enum (whoami, systeminfo, net*, privileges, services, scheduled tasks, programs).
2. Run WinPEAS → prioritise red/yellow findings.
3. Check AlwaysInstallElevated, unquoted paths, weak service perms, writable binaries.
4. Credential hunting (files, registry, DPAPI, autologon).
5. Token privileges / group abuse / potato techniques.
6. Kernel exploits only if patch level confirms and other paths fail.
7. Once local admin → dump credentials (mimikatz / secretsdump) → lateral / domain.