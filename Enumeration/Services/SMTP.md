Usual Port: 25, 587, 465

Vulnerable Setting: 
mynetworks = 0.0.0.0/0 -- Allowing everyone to authenticate and use the relay

Common Action:

-Connecting to smtp server via telnet
```
telnet <smtpserver-ip> <port>
HELO mail1.inlanefreight.htb #init session as mail1.inlanefreight.htb
EHLO mail1
```
-Verify if a user exist (chance that the server blindly reply 252 for all )
```
#After connected
VRFY <username>
```

Automated Tools: 

-Nmap (List all available commands)
```
sudo nmap 10.129.14.128 -sC -sV -p25
```

-Nmap (identify if relay is open)
```
sudo nmap 10.129.14.128 -p25 --script smtp-open-relay -v
```


Ref: https://academy.hackthebox.com/app/module/112/section/1072