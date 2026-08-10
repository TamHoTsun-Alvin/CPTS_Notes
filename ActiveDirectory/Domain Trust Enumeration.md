Sometimes, a company may decide that it is necessary to have multiple AD / Forest to best fit the companies business, or if company undergoes merge and acquisition, in this case we may need to deal with multiple AD Forest, we first would need to enumerate domains and trust types before we can start our attack
#### Trust Table Side By Side

|Transitive|Non-Transitive|
|---|---|
|Shared, 1 to many|Direct trust|
|The trust is shared with anyone in the forest|Not extended to next level child domains|
|Forest, tree-root, parent-child, and cross-link trusts are transitive|Typical for external or custom trust setups|

Trusts can be set up in two directions: one-way or two-way (bidirectional).

- `One-way trust`: Users in a `trusted` domain can access resources in a trusting domain, not vice-versa.
- `Bidirectional trust`: Users from both trusting domains can access resources in the other domain. For example, in a bidirectional trust between `INLANEFREIGHT.LOCAL` and `FREIGHTLOGISTICS.LOCAL`, users in `INLANEFREIGHT.LOCAL` would be able to access resources in `FREIGHTLOGISTICS.LOCAL`, and vice-versa.

First, we can use tools to enumerate Domain Trust Relationships:

-Using Powershell Get-ADTrust cmdlet:
```
Import-Module activedirectory
Get-ADTrust -Filter *
```

-Using PowerView to enumerate:
```
Get-DomainTrust
Get-DomainTrustMapping
```

-Using netdom to query domain Trust:
```
netdom query /domain:<domainname> trust
```

-Using netdom to query DC:
```
netdom query /domain:<domainname> dc
```

-Using netdom to query server and workstation:
```
netdom query /domain:<domainname> workstation
```

-Check Domain User in the child domain:
```
 Get-DomainUser -Domain <domain> | select SamAccountName
 #Example: 
 Get-DomainUser -Domain LOGISTICS.INLANEFREIGHT.LOCAL | select SamAccountName
```

Another easier option is to use Map Domain Trust in BloodHound

