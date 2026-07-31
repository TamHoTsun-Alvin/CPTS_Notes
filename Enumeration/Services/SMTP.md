Usual Port: 25, 587, 465

Vulnerable Setting: 

mynetworks = 0.0.0.0/0 # 
Common Action:

-Connecting to smtp server via telnet
```
telnet <smtpserver-ip> <port>
HELO mail1.inlanefreight.htb #init session with mail1.inlanefreight.htb
EHLO mail1
```
-Verify if a user exist (chance that the server blindly reply 252 for all )
```
#After connected
VRFY <username>
```
