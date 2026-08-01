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

Ref: https://academy.hackthebox.com/app/module/112/section/1242