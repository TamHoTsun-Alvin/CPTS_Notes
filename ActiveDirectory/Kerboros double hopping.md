Sometimes when we are double hopping and issue command to a second host via use of pssession or winrm, we found ourselve unable to do so due the fact that we only have the TGS Ticket sent, without our TGT Ticket the server unable to verify our identity and thus leading in failure to execute command,  we can use the following methods to include our credential so that our command can work

-Using PSCredential Object to workaround (WinRM, Evil-WinRM):

We can setup PSCredential Object to authenticate ourselves:

```
$SecPassword = ConvertTo-SecureString '<accountpassword>' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('<domain>\<user>', $SecPassword)
```

After that, we can issue PowerView commands by specifying -credential and supply $Cred in order to succed

```
get-domainuser -spn -credential $Cred | select samaccountname
```

-Using Register PSSession Configuration (GUI Access):

If we have GUI Access (via RDP) or using Enter-PSSession cmdlet, then we should use this method to perform double hopping bypass

We first enter PSSession to our first hop host (or RDP in):
```
Enter-PSSession -ComputerName ACADEMY-AEN-DEV01.INLANEFREIGHT.LOCAL -Credential inlanefreight\backupadm
```

Then, we register with our credential:
```
Register-PSSessionConfiguration -Name backupadmsess -RunAsCredential inlanefreight\backupadm
#Example
Register-PSSessionConfiguration -Name backupadmsess -RunAsCredential inlanefreight\backupadm
```