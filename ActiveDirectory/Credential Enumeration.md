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