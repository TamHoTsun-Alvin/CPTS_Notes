PRTG discovery is mainly via nmap, we would notice the string `Paessler PRTG bandwidth monitor` if the service underlying is PRTG, with the following default credentials: `prtgadmin:prtgadmin`

The attack for PRTG is mainly CVE based, on HTB material there is a authenticated command injection vulnerability demonstratedm 