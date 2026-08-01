We can utilize different tools in attempt to discover different vhost available on target system shall we suspect that vhost is in use

-Utilizing gobuster to discover vhost:
```
gobuster vhost -u http://<target_IP_address>:<port> -w <wordlist_file> --append-domain -t <thread count> -k -o <filename.txt> #-k ignores certificate error
#Example
gobuster vhost -u http://inlanefreight.htb:81 -w ~/Desktop/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain -t 20 -k -o ilfreightvhost.txt
```

Ref: https://academy.hackthebox.com/app/module/144/section/1257