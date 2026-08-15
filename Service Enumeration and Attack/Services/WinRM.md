Usual Port: 5985, 5986

Automated Tools:

-Scanning for WinRM via Nmap:
```
nmap -sV -sC <ip> -p5985,5986 --disable-arp-ping -n
```

Common Action:

-Accessing WinRM in Kali via evil-winrm:
```
evil-winrm -i <ip> -u <username> -p <password>
```

Ref: https://academy.hackthebox.com/app/module/112/section/1242