Usual Port:
SMBv1: 137,138,139
CIFS: 445

Vulnerable Setting:
browseable = yes -- Allows user to list available share within a share
read only = no / writable = yes -- Allows user to create / modify files
guest ok = yes -- Allow users to connect without specifying a password

Common Action:

-Connecting to smbshare

With rpcclient:
```
rpcclient -U "" <ip>
```
With impacket-smbclient(default, only option when pth is required)
```
python3 smbclient.py domain/username:password@target_ip
#or 
impacket-smbclient domain/username:password@target_ip
#When PTH is required
impacket-smbclient -hashes LMHASH:NTHASH domain/username@target_ip
```

-Automated Tools for General Enumeration:

enum4linux:
```
enum4linux <ip> -A
```
SMBmap:
```
smbmap -H <ip>
```
NetExec(nxc)
```
nxc smb <ip> --shares -u '<user>' -p '<pw>'
```

Common Attack / Exploit: 

SMB Enumeration:

-Using smbclient to enumerate available share:
```
smbclient -N -L //<ip>
```

-Using smbmap to enumerate available share:
```
smbmap -H <ip>
```

-Using smbmap to recursively browse directory:
```
smbmap -H <ip> -r <share>
```

-Using smbmap to read / upload file:
```
smbmap -H <ip> --download <share\filename>
smbmap -H <ip> --upload <localfname> <share\filename>
```
Ref: https://academy.hackthebox.com/app/module/112/section/1067