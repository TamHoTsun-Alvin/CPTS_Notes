Usual Port: 25, 587, 465

Vulnerable Setting: 
mynetworks = 0.0.0.0/0 -- Allowing everyone to authenticate and use the relay
Enable of EXPN and VRFY --Possible disclosure of usernames

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
EXPN <username>
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

-Using smtp-user-enum to enumerate valid user:
```
smtp-user-enum -M RCPT -U <path_to_userlist> -D <domain> -t <ip>
```

-Using o365spray to validate existence of Microsoft Office 365:
```
python3 o365spray.py --validate --domain <domain>
```

-Using o365spray to identify valid usernames:
```
python3 o365spray.py --enum -U <path_to_userlist> --domain <domain>
```

-Using o365spray to perform password spraying:
```
python3 o365spray.py --spray -U <path_to_userlist> -p '<password>' --count 1 --lockout 1 --domain msplaintext.xyz
```
Ref: https://academy.hackthebox.com/app/module/112/section/1072