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