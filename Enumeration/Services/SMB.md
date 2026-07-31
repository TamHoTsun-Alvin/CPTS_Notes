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
