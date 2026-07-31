A ping sweep can be performed to confirm the number of available host in the subnet, below is different way to approach:

via nmap:
```
sudo nmap <ip/subnet> -sn -oA tnet | grep for | cut -d" " -f5
```

via nmap (with file containing required ip):
```
sudo nmap -sn -oA tnet -iL <filename.ext> | grep for | cut -d" " -f5
```

via native linux:
```
for i in {1..255}; do ping -c 1 -W 1 192.168.1.$i | grep "from"; done
```

via Windows CMD / PowerShell:
```
for /L %i in (1,1,254) do @ping -n 1 -w 100 192.168.1.%i | findstring "Reply"
```



