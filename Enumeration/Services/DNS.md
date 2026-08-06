Usual Port: 53

Record Type:

|**DNS Record**|**Description**|
|---|---|
|`A`|Returns an IPv4 address of the requested domain as a result.|
|`AAAA`|Returns an IPv6 address of the requested domain.|
|`MX`|Returns the responsible mail servers as a result.|
|`NS`|Returns the DNS servers (nameservers) of the domain.|
|`TXT`|This record can contain various information. The all-rounder can be used, e.g., to validate the Google Search Console or validate SSL certificates. In addition, SPF and DMARC entries are set to validate mail traffic and protect it from spam.|
|`CNAME`|This record serves as an alias for another domain name. If you want the domain [www.hackthebox.eu](http://www.hackthebox.eu) to point to the same IP as hackthebox.eu, you would create an A record for hackthebox.eu and a CNAME record for [www.hackthebox.eu](http://www.hackthebox.eu).|
|`PTR`|The PTR record works the other way around (reverse lookup). It converts IP addresses into valid domain names.|
|`SOA`|Provides information about the corresponding DNS zone and email address of the administrative contact.|

Common Action:

-Obtaining SOA Record:
```
dig soa www.inlanefreight.com
```
-Obtaining ns record:
```
dig ns inlanefreight.htb @<dns-server-ip>
```
-Obtaining version (chaos style):
```
dig CH TXT version.bind <dns-server-ip>
```
-Obtaining all query:
```
dig any inlanefreight.htb @<dns-server-ip>
```
-Attempt to Zone Transfer (AXFR):
```
dig axfr inlanefreight.htb @<dns-server-ip>
```
-Subdomain Brute Forcing:
```
for sub in $(cat /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.inlanefreight.htb @<dns-server-ip> | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done
```

Automated Tools:
-Using DNSenum for subdomain bruteforcing
```
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -r
#or
dnsenum --dnsserver <dns-server-ip> --enum -p 0 -s 0 -o subdomains.txt -f /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt inlanefreight.htb
```

-Using subfinder to perform subdomain bruteforcing:
```
./subfinder -d <domain_name> -v       
```


-Using fierce to enumerate DNS servers for a domain and scan for DNS zone Transfer:
```
fierce --domain <domain>
```

Ref:https://academy.hackthebox.com/app/module/112/section/1069 / https://academy.hackthebox.com/app/module/144/section/1253 / https://academy.hackthebox.com/app/module/116/section/1512

Refer to https://academy.hackthebox.com/app/module/116/section/1512 if subdomain takeover / spoofing is needed