Usual Port: 21 / 2121

Vulnerable Setting:
Enabling Anonymous Login

Remarks:
A list of denied user can be found at /etc/ftpusers

Common Action:

-Connecting to a FTP Server:
```
ftp <ip> <port>
```

Common Attacks / Exploits:

Bruteforcing:

We can Bruteforce ftp server with Medusa in attempt to gain access to the ftp server:

Bruteforcing with medusa:
```
medusa -u <username> -P <pathtowordlist> -h <ip> -M ftp -t <threadcount> -f
```

FTP Bounce Attack:

If we are able to gain access to a FTP server, we can utilize it to perform bounce attack, one of the use is to perform an FTP Bounce scan to scan servers that is in the same network as the FTP server that we otherwise have no access:

FTP Bounce Scan with nmap:
```
nmap -Pn -v -n -p 80 -b <ftpusername>:<ftppassword>@<ftpip> <targetip>
```
Ref: https://academy.hackthebox.com/app/module/112/section/1067
