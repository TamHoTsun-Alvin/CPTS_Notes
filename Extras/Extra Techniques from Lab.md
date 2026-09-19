Fluffy: AD CS attack chain (ESC16) - Disabled SID Security Extention Globally -> Impersonation of any user 

Jeeves: Enmeration is key / SeImpersonatePrivilege

Tricks: smtp mail poisoning / nopasswd sudo abuse / racing with cronjobs

Postman: Redis Add pub ssh key to authorized / CVE for open-source payroll system / no shame in msf

POV: IIS Viewstate deserialization (YsoSerial), SeDebugPrivilege RCE (incl. CPTS Path), evil-winrm == full context

TombWatcher: ACL Abuse, ESC15, Reanimate Tombstones from deleted object

Media: Window Media Player NTLM hash leaking, SeTcbPrivilege abuse, abusing windows file junction link to gain RCE in directory

VulnCicada: NFS Credential discovery(no root squash), AD CS ESC8 Attack (relay)

streamio: Manual SQL Injection, data exfiltration with phpwrapper, winPEAs, firefox credential decrypt, password reuse, ACL Abuse, LAPS password read

Voleur: AD ACL Abuse, WSL /mnt Abuse as root, impacket-secretsdump

Administrator: AD ACL Abuse, FTP Enumeration, DCSync

Authority: SMB Anonymous Enumeration, AD CS ESC1 Attack, certipy-ad ldap-shell change password (Alternative path with PassTheCert and impacket-getST https://offsec.almond.consulting/authenticating-with-certificates-when-pkinit-is-not-supported.html)

Craft: Customized WebApp and API analysis, RestAPI interaction, OS Command Injection (Python), Interacting with MySQL, logging in as root via otp (HashiCorp Vault)

Redelegate: FTP Enumeration, KeePass Decryption, username enumeration via MSSQL, password reuse, ACL Abuse, User Privilege abuse (SeEnableDelegationPrivilege), modify `msDS-AllowedToDelegateTo` for changing password of computer object, Constrained Delegation Attack

Forest: Unauthenticated LDAP Binding / Enumeration, AS-REP Roasting, ACL Abuse (WriteDACL), DCSync Attack

Union: Manual SQL Injection Attack (Union Injection), Command Injection via Header (X-FORWARDED-FOR)

Soccer: Directory / Page Fuzzing, Default Credential Login, vhost discovery via config file, WebSocket SQL Injection, doas abuse, GTFO (dstat)

Active: Anonymous SMB Share Enumeration, GPP Password decryption (MS14-025), SMB Desktop Enumeration, Kerberoasting

Delivery: Using OsTicket to gain valid internal email, Mattermost self registration, pillaging config files, cracking root password using hashcat rules (best66)

Remote: Unauthenticated NFS Share, Exploiting Umbraco CVE, Exploiting TeamViewer 7 CVE

MetaTwo: WordPress Plugin CVE Exploit (XXE Injection), WordPress CVE Exploit (SQL Injection), Local File Inclusion, Passpie decryption

Access: Abusing Anonymous FTP, Password reuse, Converting .mdb file to .csv file, recovering mail to evolution using readpst from .pst file, executing reverse shell in another user context utilizing runas with stored cmdkey password (Optional Exercise, extract Administrator's actual password from cmdkey)

Driver: SCF NTLM Hash stealing with responder, cracking ntlmv2 hash, enumerating powershell history file, generating reverse shell with msfvenom, exploiting CVE-2019-19363 (Ricoh PCL6 Universal Driver v4.23)

Shoppy: NoSQL Injection, vhost / subdomain discovery with ffuf, simple reverse engineering with Ghidra, custom password manager, mounting host fs to docker instance, privilege escalation with docker

Manager: SMB Anonymous session, RID Cycling, Password Spraying (username=password), exploring FS with xp_dirtree, Enumerating Website backup with hidden file, Exploiting ESC7