Usual Port: 1433

Common Action:

-Connecting to MSSQL using impacket-mssqlclient
```
impacket-mssqlclient <username>@<ip> -windows-auth
#or
python3 mssqlclient.py <username>@<ip> -windows-auth
```

Automated Tools:

-Using nmap for script scan:
```
sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=<port>,mssql.username=<username>,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248
```