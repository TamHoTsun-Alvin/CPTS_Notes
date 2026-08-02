For different kind of protocols, we can use different Tools to perform password bruteforcing / spraying attack

Using NetExec(SMB, SSH, LDAP, FTP, WMI, WinRM, RDP, VNC, MSSQL, and NFS):

-NetExec Basic syntax:
```
netexec <protocol> <target-IP> -u <user or userlist> -p <password or passwordlist> -t <threadcount>
```

-Check if user account exist in AD via LDAP:
```
nxc ldap <target> -u <username> -p '' --no-smb
```

Using hydra(ssh, )