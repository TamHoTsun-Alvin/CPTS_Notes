A ping sweep can be performed to confirm the number of available host in the subnet, below is different way to approach:

via nmap:
```
sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f5
```