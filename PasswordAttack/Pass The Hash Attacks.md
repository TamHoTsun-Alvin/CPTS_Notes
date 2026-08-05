From time to time, we will encounter some hash that cannot be cracked using wordlist due to user having a good practice in making their password. However, if we obtained the hash of an account that have administrative privilege, we can attempt to perform PTH attacks by authenticating with the hash instead of the cleartext password.

-Passing the hash with mimikatz:
We can use mimikatz to spawn process in other user's context if we obtained their hash
```
mimikatz.exe
privilege::debug
sekurlsa::pth /user:<username> /<rc4/ntlm>:<hash> /domain:<domain_name> /run:<process to spawn>
exit
```

-Passing the hash with PowerShell Invoke-TheHash:
We can 