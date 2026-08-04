In principal, extracting password requires 2 file, /etc/password and /etc/shadow, it is not likely but /etc/password may contain some users hashes, while /etc/shadow requires admin privilege to read, once we transfer both file to our attack host, we can use unshadow to process these files and use hashcat to crack them.

Using unshadow:
```
victim$ sudo cp /etc/passwd /tmp/passwd.bak 
victim$ sudo cp /etc/shadow /tmp/shadow.bak 
Avalon112@htb[/htb]$ unshadow /tmp/passwd.bak /tmp/shadow.bak > /tmp/unshadowed.hashes
```