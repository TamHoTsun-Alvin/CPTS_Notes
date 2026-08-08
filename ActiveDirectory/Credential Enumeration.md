After gaining a foothold, next we should perform credential enumeration, aside from the Cred Hunting specified on [[Credential Hunting]], we can also do something regarding AD credentials and see if we could get more

When we got ourselves a foothold with a valid pair of credentials, the first thing we wanna do is to enumerate for more information, we can do this with the help of NetExec

-Enumerating valid Users with NetExec:
```
sudo nxc smb <dcip> -u <username> -p <password> --users
#A list of user and their badpasswordcount will be outputed, which helps us spray without locking the account
```

-Enumerating domain groups with NetExec:
```
sudo nxc smb <dcip> -u <username> -p <password> --groups
```

-Enumerating valid smb shares on remote host:
```
sudo nxc smb <dcip> -u <username> -p <password> --shares
```

-Using nxc to crawl through shares we have read access to:
```
sudo nxc smb <dcip> -u <username> -p <password> -M spider_plus --share '<sharename>'
```
We can also enumerate SMB with different kind of tools, refer to [[Enumeration/Services/SMB]] for more detail

If we are able to get a rpcclient null session, without the need of valid credential we can enumerate domain users and get respective username and rid:
```
rpcclient -U "" -N <dcip> #Start a null session
enumdomusers
```

Another tool we can use is Windapsearch, which utilize ldap queries to enumerate domain users, a valid credentials is required to run this tool:
```
python3 windapsearch.py --dc-ip <dcip> -u <username>@<domainname> -p <password>
#We can supply -PU or -DA to search for privileged users and domain admins respectively
```

Last but not least, we can do a bloodhound enumeration from Linux with the help of bloodhound-python, this act exactly like running sharphound at a window host, we can zip the resulting files and use it at BloodHound:

```
sudo bloodhound-python -u '<username>' -p '<password>' -ns <dcip> -d <domainname> -c all 
zip -r <filename>.zip *.json
```

Then we can upload the zip file to BloodHound.