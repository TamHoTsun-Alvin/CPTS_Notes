Sometimes, we can obtain default login credentials via grabbing lsass or registries, this would appear as DefaultLogin or Unknown User, we can check it via 2 means:

CMD:
```
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultUserName
```

PowerShell:
```
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" | Select-Object AutoAdminLogon, DefaultDomainName, DefaultUserName, DefaultPassword
```

```
Get-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\' -Name "DefaultUserName"
```