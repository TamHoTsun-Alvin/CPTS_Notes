Usual Port: 873

Automated Tools:

-Scanning for rsync via nmap:
```
sudo nmap -sV -p <port> <ip>
```

Common Action:

-Accessing rsync via netcat:
```
nc -nv <ip> <port>
```
-Enumerate specific share in rsync via rsync:
```
rsync -av --list-only rsync://<ip>/<share>
```

Ref: https://academy.hackthebox.com/app/module/112/section/1240