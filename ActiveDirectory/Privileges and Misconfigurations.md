When we are able to obtain credentials for an account, their is a chance that this user would have some permission that can allow us to further access / lateral movement or pilaging on different machines, below is 3 privileges that is interesting to us:

CanRDP
CanPSRemote
SQLAdmin


-CanRDP

CanRDP allows us to access machines with RDP and login to it, this is useful to us as now we can launch different attacks, obtaining credentials for more privileged user and potentially pillage other hosts for sensitive data and information

-Enumerate RDP Group using PowerView:
```
Get-NetLocalGroupMember -ComputerName <computername> -GroupName "Remote Desktop Users"
```

We can also check for such privileges using BloodHound

-CanPSRemote

CanPSRemote gave us access to WinRM to one of more host, which in return allows us to use WinRM to connect to the hosts (or evil-winrm from kali)

-Enumerate RMUsers Group
```
Get-NetLocalGroupMember -ComputerName <computername> -GroupName "Remote Management Users"
```

The following cypher query can be issued on BloodHound to query for users with such access:
```
MATCH p1=shortestPath((u1:User)-[r1:MemberOf*1..]->(g1:Group)) MATCH p2=(u1)-[:CanPSRemote*1..]->(c:Computer) RETURN p2
```

Below is an example to launch WinRM session from windows:
```
PS C:\htb> $password = ConvertTo-SecureString "Klmcargo2" -AsPlainText -Force
PS C:\htb> $cred = new-object System.Management.Automation.PSCredential ("INLANEFREIGHT\forend", $password)
PS C:\htb> Enter-PSSession -ComputerName ACADEMY-EA-MS01 -Credential $cred

[ACADEMY-EA-MS01]: PS C:\Users\forend\Documents> hostname
ACADEMY-EA-MS01
[ACADEMY-EA-MS01]: PS C:\Users\forend\Documents> Exit-PSSession
PS C:\htb> 
```

-SQLAdmin

This privileges is pretty self explanatory, below we will cover how to discover SQL Instance and find out SQLAdmin User

-Locate user that have SQL Admin Rights at BloodHound:
```
MATCH p1=shortestPath((u1:User)-[r1:MemberOf*1..]->(g1:Group)) MATCH p2=(u1)-[:SQLAdmin*1..]->(c:Computer) RETURN p2
```

-Locate MSSQL instance with PowerUpSQL:
```
PS C:\htb> cd .\PowerUpSQL\
PS C:\htb>  Import-Module .\PowerUpSQL.ps1
PS C:\htb>  Get-SQLInstanceDomain
```
