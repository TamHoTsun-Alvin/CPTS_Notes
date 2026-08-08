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

If we are able to get a rpcclient null session, without the need of valid credential we can enumerate domain users and get respective username and rid:
```
rpcclient -U "" -N <dcip> #Start a null session
enumdomusers
```

Another tool we can use is Windapsearch, which utilize ldap queries to enumerate domain users, a valid credentials is required to run this tool:
```
python3 windapsearch.py --dc-ip <dcip> -u <username>@<domainname> -p <password>
#We can supply -PU or -DA to search for privileged users and domain admins respectively
```

Last but not least, we can do a bloodhound enumeration from Linux with the help of bloodhound-python, this act exactly like running sharphound at a window host, we can zip the resulting files and use it at BloodHound:

```
sudo bloodhound-python -u '<username>' -p '<password>' -ns <dcip> -d <domainname> -c all 
zip -r <filename>.zip *.json
```

Then we can upload the zip file to BloodHound.

If we have means to access windows, we then can utilize different Tools to enumerate domain users.

-Using the ActiveDirectory PowerShell Module

If we for some reason are unable to load our tools on it and as a last resort, we can simply use PS ActiveDirectory Module to perform manual enumeration, if other tools are available it is recommended that we use them instead

-Utilizing ActiveDirectory PS Module:
```
Import-Module ActiveDirectory
Get-ADDomain #Get Domain Info

Get-ADUser -Identity "<name>" -Properties * #Get info for specific user

Get-ADUser -Filter * #Get all AD User

Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName #Get all AD User with SPN

Get-ADTrust -Filter * #Get AD Trust Relationships

Get-ADGroup -Filter * | select name #Get all AD Group and show name

Get-ADGroup -Identity "<groupname>" #Get Group Info

Get-ADGroupMember -Identity "<groupname>" #Get Group Member
```

-Utilizing PowerView

If we are able to transfer Tools onto the foothold, we can try utilize PowerView by importing it to our PowerShell session:

| **Command**                         | **Description**                                                                            |
| ----------------------------------- | ------------------------------------------------------------------------------------------ |
| `Export-PowerViewCSV`               | Append results to a CSV file                                                               |
| `ConvertTo-SID`                     | Convert a User or group name to its SID value                                              |
| `Get-DomainSPNTicket`               | Requests the Kerberos ticket for a specified Service Principal Name (SPN) account          |
| **Domain/LDAP Functions:**          |                                                                                            |
| `Get-Domain`                        | Will return the AD object for the current (or specified) domain                            |
| `Get-DomainController`              | Return a list of the Domain Controllers for the specified domain                           |
| `Get-DomainUser`                    | Will return all users or specific user objects in AD                                       |
| `Get-DomainComputer`                | Will return all computers or specific computer objects in AD                               |
| `Get-DomainGroup`                   | Will return all groups or specific group objects in AD                                     |
| `Get-DomainOU`                      | Search for all or specific OU objects in AD                                                |
| `Find-InterestingDomainAcl`         | Finds object ACLs in the domain with modification rights set to non-built in objects       |
| `Get-DomainGroupMember`             | Will return the members of a specific domain group                                         |
| `Get-DomainFileServer`              | Returns a list of servers likely functioning as file servers                               |
| `Get-DomainDFSShare`                | Returns a list of all distributed file systems for the current (or specified) domain       |
| **GPO Functions:**                  |                                                                                            |
| `Get-DomainGPO`                     | Will return all GPOs or specific GPO objects in AD                                         |
| `Get-DomainPolicy`                  | Returns the default domain policy or the domain controller policy for the current domain   |
| **Computer Enumeration Functions:** |                                                                                            |
| `Get-NetLocalGroup`                 | Enumerates local groups on the local or a remote machine                                   |
| `Get-NetLocalGroupMember`           | Enumerates members of a specific local group                                               |
| `Get-NetShare`                      | Returns open shares on the local (or a remote) machine                                     |
| `Get-NetSession`                    | Will return session information for the local (or a remote) machine                        |
| `Test-AdminAccess`                  | Tests if the current user has administrative access to the local (or a remote) machine     |
| **Threaded 'Meta'-Functions:**      |                                                                                            |
| `Find-DomainUserLocation`           | Finds machines where specific users are logged in                                          |
| `Find-DomainShare`                  | Finds reachable shares on domain machines                                                  |
| `Find-InterestingDomainShareFile`   | Searches for files matching specific criteria on readable shares in the domain             |
| `Find-LocalAdminAccess`             | Find machines on the local domain where the current user has local administrator access    |
| **Domain Trust Functions:**         |                                                                                            |
| `Get-DomainTrust`                   | Returns domain trusts for the current domain or a specified domain                         |
| `Get-ForestTrust`                   | Returns all forest trusts for the current forest or a specified forest                     |
| `Get-DomainForeignUser`             | Enumerates users who are in groups outside of the user's domain                            |
| `Get-DomainForeignGroupMember`      | Enumerates groups with users outside of the group's domain and returns each foreign member |
| `Get-DomainTrustMapping`            | Will enumerate all trusts for the current domain and any others seen.                      |
Below is some examples to use these commands in action:

-Enumerate a user's information in a detailed manner:
```
Get-DomainUser -Identity <username> -Domain <domainname> | Select-Object -Property name,samaccountname,description,memberof,whencreated,pwdlastset,lastlogontimestamp,accountexpires,admincount,userprincipalname,serviceprincipalname,useraccountcontrol
```

-Get Members of a certain group:
```
Get-DomainGroupMember -Identity "<groupname>" -Recurse
```

-Testing Local Admin Access:
```
Test-AdminAccess -ComputerName <computername>
```

