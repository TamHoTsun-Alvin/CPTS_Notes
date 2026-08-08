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

If we are able to get a rpcclient null session, without the need of valid credential we can enumerate