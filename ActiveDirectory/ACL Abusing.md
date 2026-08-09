In an AD Environment, to facilitate business practice some personal naturally requires higher privilege and administrative action against other accounts (I.E. HelpDesk Member having the necessary permission to set other user's password), if we found ourself able to gain their credentials, then we are able to abuse that and make changes that allow us to further enumerate or perform lateral movement (e.g. Acquiring credentials for DHoover (HelpDeskL2 -> ForceChangePassword), under the context of DHoover we change password of the account TEdison(GenericAll) to "relinkgbf1!" , then login to Add TEdison / DHoover or any account we have to the Domain Admin Group)

Below is some common abuse taken from HTB Website:
![[Pasted image 20260809171957.png]]
- [ForceChangePassword](https://bloodhound.specterops.io/resources/edges/force-change-password#forcechangepassword) - gives us the right to reset a user's password without first knowing their password (should be used cautiously and typically best to consult our client before resetting passwords).
- [GenericWrite](https://bloodhound.specterops.io/resources/edges/generic-write#genericwrite) - gives us the right to write to any non-protected attribute on an object. If we have this access over a user, we could assign them an SPN and perform a Kerberoasting attack (which relies on the target account having a weak password set). Over a group means we could add ourselves or another security principal to a given group. Finally, if we have this access over a computer object, we could perform a resource-based constrained delegation attack which is outside the scope of this module.
- [AddSelf](https://bloodhound.specterops.io/resources/edges/add-self#addself) - shows security groups that a user can add themselves to.
- [GenericAll](https://bloodhound.specterops.io/resources/edges/generic-all#genericall) - this grants us full control over a target object. Again, depending on if this is granted over a user or group, we could modify group membership, force change a password, or perform a targeted Kerberoasting attack. If we have this access over a computer object and the [Local Administrator Password Solution (LAPS)](https://www.microsoft.com/en-us/download/details.aspx?id=46899) is in use in the environment, we can read the LAPS password and gain local admin access to the machine which may aid us in lateral movement or privilege escalation in the domain if we can obtain privileged controls or gain some sort of privileged access.

To abuse ACL, we first need to perform enumeration to see what kind of ACL entries a user have

-Enumerating ACL with PowerView
```
Import-Module .\PowerView.ps1
$sid = Convert-NameToSid <username>
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid}
```

-Investigate Specific Domain Groups with PowerView:
```
Get-DomainGroup -Identity "<groupname>" | select memberof #Mark down CN, which will be used to query for more information
```

-Investigate information of specific Domain Groups with Powerview:
```
$groupsid = Convert-NameToSid "<group"
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $groupsid} -Verbose
```
