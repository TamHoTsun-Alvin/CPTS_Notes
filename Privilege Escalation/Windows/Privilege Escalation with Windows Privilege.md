Sometimes, we can perform Privilege Escalation with built in rights and privileges in windows, below is a list of some of the windows groups and description:

|**Group**|**Description**|
|---|---|
|Default Administrators|Domain Admins and Enterprise Admins are "super" groups.|
|Server Operators|Members can modify services, access SMB shares, and backup files.|
|Backup Operators|Members are allowed to log onto DCs locally and should be considered Domain Admins. They can make shadow copies of the SAM/NTDS database, read the registry remotely, and access the file system on the DC via SMB. This group is sometimes added to the local Backup Operators group on non-DCs.|
|Print Operators|Members can log on to DCs locally and "trick" Windows into loading a malicious driver.|
|Hyper-V Administrators|If there are virtual DCs, any virtualization admins, such as members of Hyper-V Administrators, should be considered Domain Admins.|
|Account Operators|Members can modify non-protected accounts and groups in the domain.|
|Remote Desktop Users|Members are not given any useful permissions by default but are often granted additional rights such as `Allow Login Through Remote Desktop Services` and can move laterally using the RDP protocol.|
|Remote Management Users|Members can log on to DCs with PSRemoting (This group is sometimes added to the local remote management group on non-DCs).|
|Group Policy Creator Owners|Members can create new GPOs but would need to be delegated additional permissions to link GPOs to a container such as a domain or OU.|
|Schema Admins|Members can modify the Active Directory schema structure and backdoor any to-be-created Group/GPO by adding a compromised account to the default object ACL.|
|DNS Admins|Members can load a DLL on a DC, but do not have the necessary permissions to restart the DNS server. They can load a malicious DLL and wait for a reboot as a persistence mechanism. Loading a DLL will often result in the service crashing. A more reliable way to exploit this group is to [create a WPAD record](https://web.archive.org/web/20231115070425/https://cube0x0.github.io/Pocing-Beyond-DA/).|

Below is a list of some User Rights:

|Setting [Constant](https://docs.microsoft.com/en-us/windows/win32/secauthz/privilege-constants)|Setting Name|Standard Assignment|Description|
|---|---|---|---|
|SeNetworkLogonRight|[Access this computer from the network](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/access-this-computer-from-the-network)|Administrators, Authenticated Users|Determines which users can connect to the device from the network. This is required by network protocols such as SMB, NetBIOS, CIFS, and COM+.|
|SeRemoteInteractiveLogonRight|[Allow log on through Remote Desktop Services](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services)|Administrators, Remote Desktop Users|This policy setting determines which users or groups can access the login screen of a remote device through a Remote Desktop Services connection. A user can establish a Remote Desktop Services connection to a particular server but not be able to log on to the console of that same server.|
|SeBackupPrivilege|[Back up files and directories](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/back-up-files-and-directories)|Administrators|This user right determines which users can bypass file and directory, registry, and other persistent object permissions for the purposes of backing up the system.|
|SeSecurityPrivilege|[Manage auditing and security log](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/manage-auditing-and-security-log)|Administrators|This policy setting determines which users can specify object access audit options for individual resources such as files, Active Directory objects, and registry keys. These objects specify their system access control lists (SACL). A user assigned this user right can also view and clear the Security log in Event Viewer.|
|SeTakeOwnershipPrivilege|[Take ownership of files or other objects](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/take-ownership-of-files-or-other-objects)|Administrators|This policy setting determines which users can take ownership of any securable object in the device, including Active Directory objects, NTFS files and folders, printers, registry keys, services, processes, and threads.|
|SeDebugPrivilege|[Debug programs](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/debug-programs)|Administrators|This policy setting determines which users can attach to or open any process, even a process they do not own. Developers who are debugging their applications do not need this user right. Developers who are debugging new system components need this user right. This user right provides access to sensitive and critical operating system components.|
|SeImpersonatePrivilege|[Impersonate a client after authentication](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/impersonate-a-client-after-authentication)|Administrators, Local Service, Network Service, Service|This policy setting determines which programs are allowed to impersonate a user or another specified account and act on behalf of the user.|
|SeLoadDriverPrivilege|[Load and unload device drivers](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/load-and-unload-device-drivers)|Administrators|This policy setting determines which users can dynamically load and unload device drivers. This user right is not required if a signed driver for the new hardware already exists in the driver.cab file on the device. Device drivers run as highly privileged code.|
|SeRestorePrivilege|[Restore files and directories](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/restore-files-and-directories)|Administrators|This security setting determines which users can bypass file, directory, registry, and other persistent object permissions when they restore backed up files and directories. It determines which users can set valid security principals as the owner of an object.|
|SeTcbPrivilege|[Act as part of the operating system](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/security-policy-settings/act-as-part-of-the-operating-system)|Administrators, Local Service, Network Service, Service|This security setting determines whether a process can assume the identity of any user and, through this, obtain access to resources that the targeted user is permitted to access (impersonation). This may be assigned to antivirus or backup tools that need the ability to access all system files for scans or backups. This privilege should be reserved for service accounts requiring this access for legitimate activities.|
Sometimes, accounts are assigned privileges but comes in disabled state, it means we are indeed assigned such privileges but can't directly use it, we can use the following script https://github.com/fashionproof/EnableAllTokenPrivs to enable all privileges assigned to us

Privilege Escalation with SeImpersonate and SeAssignPrimaryToken:

SeImpersonate privileges allows a process to pretend itself to be another account after authentication, often this privilege will be found on SQL service accounts, if we are able to gain RCE on accounts like this, we can obtain a reverse shell and catch it with metasploit, then utilize metesploit getsystem, we can also manually exploit respective potato attack module.

An example that does not require metasploit is also available at https://academy.hackthebox.com/app/module/67/section/607, but it requires xp_cmdshell and file dropping as we need to transfer https://github.com/ohpe/juicy-potato/releases and nc to target machine, another example of PrintSpoofer and RoguePotato is also available https://academy.hackthebox.com/app/module/67/section/607

Privilege Escalation with SeDebugPrivilege:

User with SeDebugPrivilege are allowed to perform actions against programs for debug purpose, including but not limited to reading, attaching, dumping memories, including process that is critical like LSASS.

We can use procdump.exe from sysinternals to dump process, using LSASS as example:
```
procdump.exe -accepteula -ma lsass.exe lsass.dmp
```

Or we can use other method we know already like taskmgr dumping to create a dump for LSASS, then we can use mimikatz.exe to extract hashes
```
C:\htb> mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #19041 Sep 18 2020 19:18:29
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz # log
Using 'mimikatz.log' for logfile : OK

mimikatz # sekurlsa::minidump lsass.dmp
Switch to MINIDUMP : 'lsass.dmp'

mimikatz # sekurlsa::logonpasswords
Opening : 'lsass.dmp' file for minidump...
<snip>
```

We can also archive RCE with SeDebugPrivilege, using https://github.com/decoder-it/psgetsystem, we first find process PID which is run as NT AUTHORITY\SYSTEM, then we can input command and args, this would allow us to run command as SYSTEM, we could use it to spawn a cmd (start cmd) or execute other code

Privilege Escalation with SeTakeOwnershipPrivilege:

SeTakeOwnershipPrivileges allows a user to take ownership of any securable objects, from AD objects, files, folders, regkeys or even service and process, we often won't be encountering standard user account with this privilege, but the service account that's running for backup jobs and VSS snapshots would probably be assigned this privilege, a combination of SeBackupPrivilege, SeRestorePrivilege and SeSecurityPrivilege may also be seen to avoid giving the account full admin privilege.

We maybe not able to directly archive escalation with this privilege, but we definitely can use it to take ownership of files and allow us to gain more info on the system

Taking ownership of a file (in case GUI is not available):
```
takeown <filepath>
Example:
takeown /f 'C:\Department Shares\Private\IT\cred.txt'
```

After taking ownership, we can modify the file ACL, which we can now give ourselves full access:
```
icalcs '<pathtofile>' /grant <username>:F
#Example:
icacls 'C:\Department Shares\Private\IT\cred.txt' /grant htb-student:F
```

Below is a list of file of interest to utilize such:
