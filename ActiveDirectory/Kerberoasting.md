When we acquired valid credentials for user in the AD, we then can attempt to perform Kerberoasting, since all users in a domain can request Kerboros ticket for any service account in the domain, as service account are often configured to have high privilege, the ticket itself won't do you good, however the ticket is encrypted with the service account's NTLM hash, therefore we can potentially obtain such hash and crack it to reveal the cleartext password of the service account, hence granting us potentially elevated privilege

We first start by introducing methods to obtain TGS from a linux attack host:

-Using impacket-getuserspns to gather all SPN account in the domain:

```
impacket-getuserspns -dc-ip <dcip> <domainname>/<username>
```

-Using impacket-getuserspns to request all TGS ticket:
```
impacket-getuserspns -dc-ip <dcip> <domainname>/<username> -request 
#We can specify user <username> at the end if we are only interested in one SPN
```