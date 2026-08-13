SQLMap is a powerful pentesting tools that specialize in detecting and exploiting SQLi flaws, proper usage of it may greatly increase the efficiency of our pentesting process, below is a list of DBMS it support:

|`MySQL`|`Oracle`|`PostgreSQL`|`Microsoft SQL Server`|
|`SQLite`|`IBM DB2`|`Microsoft Access`|`Firebird`|
|`Sybase`|`SAP MaxDB`|`Informix`|`MariaDB`|
|`HSQLDB`|`CockroachDB`|`TiDB`|`MemSQL`|
|`H2`|`MonetDB`|`Apache Derby`|`Amazon Redshift`|
|`Vertica`, `Mckoi`|`Presto`|`Altibase`|`MimerSQL`|
|`CrateDB`|`Greenplum`|`Drizzle`|`Apache Ignite`|
|`Cubrid`|`InterSystems Cache`|`IRIS`|`eXtremeDB`|
|`FrontBase`||||

SQLMap support different SQL Injection type, including:
-Boolean-based Blind
-Error-based
-Union query-based (fastest)
-Stacked queries
-Time-based blind
-Inline Queries

Refer to https://academy.hackthebox.com/app/module/58/section/696 to see output description

-Ways to use SQLMap:

The best way to use sqlmap is to identify the request that we are interested using burp or firefox, then we right click and select copy as curl, paste it to terminal and change curl to sqlmap. Notice that there needs to be a parameter value for SQLMap to investigate (e.g. https://example.com/?id=1)  or else we need to specify options for automatic parameter finding

For POST Request, simply follows the above or input it in the following way:
```
sqlmap 'http://www.example.com/' --data 'uid=1&name=test'
```
If we only wish to test some of the provided parameter, mark a * at the end like the following:
```
sqlmap 'http://www.example.com/' --data 'uid=1*&name=test'
```

It also support we provide a HTTP request from a file, to do so, we use burp suite and copy the entire HTTP Request, then save it to a file, then we use the following syntax to fire SQLMap:
```
sqlmap -r rep.txt
```

we can include specific cookie at our request by specifying:
```
--cookie='<name>=<value>' 
```
at the end of our request or utilize header -H, below are example:
```
sqlmap ... --cookie='PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
#or
sqlmap ... -H='Cookie:PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
```

Remember to also add --random-agent at the end, as many website drop sqlmap default user agent nowadays, alternative methods 

When encountering errors, we can do the verbose output by specifying -v, enable error parsing by specifying --parse-errors, output traffic to a file by specifying -t followed by file path, or we can use a proxy such as burp to review all the request made.

Attack Tuning:

We can fine tune our attacks with the following parameters:

--prefix and --suffix:

use it when our payload need to be have some prefix and suffix to be considered valid

--level and --risk

by default, only techniques with level 1 and risk 1 is used, however they can be extended till level 5 and risk 3, notice that when level goes higher, method that is less likely to succeed will be used and as risk goes higher, chances of db entry loss and or DNS

Customized detection of TRUE response:

We can fixate the detection of true responses by using status code (--code=xxx), titles (--title) or string (--string=xxx)

Database enumeration:

After successful detection of SQLi vulnerability, we may start enumerating by detecting db version, current user, current db name and whether current user is DBA, we can do it by following the example:
```
sqlmap -u "http://www.example.com/?id=1" --banner --current-user --current-db --is-dba
```

Table Enumeration:

After knowing what current DB we are in, we can enumerate tables we have by using --tables
```
sqlmap -u "http://www.example.com/?id=1" --tables -D testdb
```

After knowing what table we have, we can dump it with --dump option and specify table name with -T
```
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb
```
We can also specify the only column to dump with -C option:
```
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb -C name,surname
```
We can only enumerate a specific range of rows by specifying --start and --stop:
```
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb --start=2 --stop=3
```
We can enumerate rows 