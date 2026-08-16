We can enumerate Named Pipes with pipelist.exe from sysinternals suite:
```
pipelist.exe /accepteula
```

After that, we can use acesschk.exe from sysinternals to see it's permission for different user:
```
accesschk.exe /accepteula \\.\Pipe\lsass -v
```

if we found a pipe with lax permission, we might be able to use it

Also, there are vulnerabilities regarding named pipes, like the windscribe named pipe privilege escalation https://www.exploit-db.com/exploits/48021