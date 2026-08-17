Sometimes in a well patched and configured systems, we may rely on vulnerable services if users are permitted to install software or third party applications.

We can first enumerate installed application:
```
C:\htb> wmic product get name

Name
Microsoft Visual C++ 2019 X64 Minimum Runtime - 14.28.29910
Update for Windows 10 for x64-based Systems (KB4023057)
Microsoft Visual C++ 2019 X86 Additional Runtime - 14.24.28127
VMware Tools
Druva inSync 6.6.3
Microsoft Update Health Tools
Microsoft Visual C++ 2019 X64 Additional Runtime - 14.28.29910
Update for Windows 10 for x64-based Systems (KB4480730)
Microsoft Visual C++ 2019 X86 Minimum Runtime - 14.24.28127
```

In HTB example, Druva inSync 6.6.3 is exploited, we usually need to search online for whether the installed application contains vulnerability, but if for some reason Druva inSync 6.6.3 is encountered, we can refer to https://academy.hackthebox.com/app/module/67/section/910