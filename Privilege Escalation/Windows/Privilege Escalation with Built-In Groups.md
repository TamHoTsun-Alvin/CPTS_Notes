Sometimes, we can utilize account with specific Built-In Groups to archive privilege escalation or as opener to path towards other account.

One Example would be the Backup Operators Account, we first run whoami /priv to confirm we have respective privilege, we afterward need to load https://github.com/giuliano108/SeBackupPrivilege this tools over to target.

We import them both in PS, then enable privilege by following:
```
PS C:\htb> Set-SeBackupPrivilege
PS C:\htb> Get-SeBackupPrivilege
```

Now we can copy any protected file which can expose sensitive information.

However, there is more of it, 