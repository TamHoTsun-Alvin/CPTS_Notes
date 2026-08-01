On RDP, we can expose a directory in our FS as network drive and use it to transfer file bidirectionally, if such is possible this is the most convenient method to do bidirectional transfer

rdesktop:
```
rdesktop <ip> -d <domain> -u <username> -p '<password>' -r disk:<diskname>='/<directory>'
```
xfreerdp:
```
xfreerdp /v:<ip> /d:<domain> /u:<username> /p:'<password>' /drive:<drivename>,<directory> /dynamic-resolution /timeout:10000
```
Remmina:
```
1.Right click on connecting panel and select edit
2.On the share folder option, type in absolute path to path to be exposed
```

Ref: https://academy.hackthebox.com/app/module/24/section/161