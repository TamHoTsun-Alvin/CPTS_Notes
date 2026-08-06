Usual Port: 1433

Common Action:

-Connecting to MSSQL using impacket-mssqlclient
```
impacket-mssqlclient <username>@<ip> -windows-auth
#or
python3 mssqlclient.py <username>@<ip> -windows-auth
```
Alternative - Use sqsh:
```
sqsh -S <ip>
```
Automated Tools:

-Using nmap for script scan:
```
sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=<port>,mssql.username=<username>,mssql.password=<password/blank>,mssql.instance-name=<instancename> -sV -p <port> <ip>
```

-Using Metasploit for recon:
```
#in msfconsole
use scanner/mssql/mssql_ping
set rhosts <ip>
exploit
```

Ref: https://academy.hackthebox.com/app/module/112/section/1246