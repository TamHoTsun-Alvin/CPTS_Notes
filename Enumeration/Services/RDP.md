Usual Port: 3389

Automated Tools:

-Scanning for RDP with nmap:
```
nmap -sV -sC 10.129.201.248 -p3389 --script rdp*
```
-Check for RDP security issue with RDP Security Check:
```
sudo cpan
git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check
./rdp-sec-check.pl <ip>
```

Common Action:

Initiating Session with Remmina (Preferred):
```
remmina
#Opens up GUI, continue with GUI
```

Initiating Session with xfreerdp:
```
xfreerdp /v:<ip> /u:<username> /p:<password> /dynamic-resolution /timeout:10000
#Using hash to login is also possible
```

Initiating Session with rdesktop:
```
rdesktop -u <username> -p <password> <ip>
```
Refer to [[Attacking Remote Service]] for methods to perform Password attack / spraying

Post Access:



After we are logged in, if we have SYSTEM privilege, it is possible 
Ref: https://academy.hackthebox.com/app/module/112/section/1242