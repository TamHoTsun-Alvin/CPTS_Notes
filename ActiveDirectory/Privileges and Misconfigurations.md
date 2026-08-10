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

The f