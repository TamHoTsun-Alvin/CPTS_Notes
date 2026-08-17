## 1. AD Tools — Require Transfer to Host (Windows foothold)

| Tool / Script                   | Purpose                                                          | Acquisition                                                                                                                                                      |
| ------------------------------- | ---------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| PowerView.ps1                   | PS module for AD/domain/user/group/ACL/trust enumeration & abuse | [https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1) |
| SharpView.exe                   | Compiled .NET port of PowerView (use when PS constrained)        | [https://github.com/tevora-threat/SharpView](https://github.com/tevora-threat/SharpView)                                                                         |
| Snaffler.exe                    | Crawl reachable files/shares for interesting credentials         | [https://github.com/SnaffCon/Snaffler/releases](https://github.com/SnaffCon/Snaffler/releases)                                                                   |
| SharpHound.exe                  | BloodHound data collector (run on Windows foothold)              | [https://github.com/BloodHoundAD/SharpHound/releases](https://github.com/BloodHoundAD/SharpHound/releases)                                                       |
| PowerUpSQL.ps1                  | Locate/enumerate MSSQL instances in domain; SQLAdmin abuse       | [https://github.com/NetSPI/PowerUpSQL](https://github.com/NetSPI/PowerUpSQL)                                                                                     |
| Rubeus.exe                      | Kerberos abuse (kerberoast/PTT/asktgt/dump/s4u)                  | [https://github.com/GhostPack/Rubeus/releases](https://github.com/GhostPack/Rubeus/releases)                                                                     |
| mimikatz.exe                    | DCSync, golden/PTT, LSASS dump, LSA secrets, PTH                 | [https://github.com/gentilkiwi/mimikatz/releases](https://github.com/gentilkiwi/mimikatz/releases)                                                               |
| Invoke-TheHash.psd1             | SMB/WMI PTH command execution across domain                      | [https://github.com/Kevin-Robertson/Invoke-TheHash](https://github.com/Kevin-Robertson/Invoke-TheHash)                                                           |
| DomainPasswordSpray.ps1         | Internal password spray from Windows foothold                    | [https://github.com/dafthack/DomainPasswordSpray](https://github.com/dafthack/DomainPasswordSpray)                                                               |
| DSInternals.psd1                | Read NTDS.dit offline; extract hashes from ntds+bootkey          | [https://github.com/MichaelGrafnetter/DSInternals](https://github.com/MichaelGrafnetter/DSInternals)                                                             |
| SeBackupPrivilege (Utils)       | Enable/use SeBackupPrivilege to copy NTDS.dit on DC              | [https://github.com/giuliano108/SeBackupPrivilege](https://github.com/giuliano108/SeBackupPrivilege)                                                             |
| EnableAllTokenPrivs             | Re-enable disabled assigned privileges (e.g. SeImpersonate)      | [https://github.com/fashionproof/EnableAllTokenPrivs](https://github.com/fashionproof/EnableAllTokenPrivs)                                                       |
| psgetsystem                     | RCE as SYSTEM via SeDebugPrivilege parent-process spoofing       | [https://github.com/decoder-it/psgetsystem](https://github.com/decoder-it/psgetsystem)                                                                           |
| SharpUp.exe                     | Audit weak service/file/registry ACLs (GhostPack)                | [https://github.com/GhostPack/SharpUp](https://github.com/GhostPack/SharpUp)                                                                                     |
| WinPEAS.exe                     | Automated Windows PE enumeration                                 | [https://github.com/peass-ng/PEASS-ng/releases](https://github.com/peass-ng/PEASS-ng/releases) (winPEAS)                                                         |
| accesschk.exe / accesschk64.exe | Check effective perms on services/files/registry/pipes           | Sysinternals — [https://learn.microsoft.com/sysinternals/downloads/accesschk](https://learn.microsoft.com/sysinternals/downloads/accesschk)                      |
| pipelist.exe                    | Enumerate named pipes                                            | Sysinternals — [https://learn.microsoft.com/sysinternals/downloads/pipelist](https://learn.microsoft.com/sysinternals/downloads/pipelist)                        |
| PsService.exe                   | Query/modify service ACLs (Server Operators abuse)               | Sysinternals — [https://learn.microsoft.com/sysinternals/downloads/psservice](https://learn.microsoft.com/sysinternals/downloads/psservice)                      |
| procdump.exe                    | Dump LSASS for offline hash extraction                           | Sysinternals — [https://learn.microsoft.com/sysinternals/downloads/procdump](https://learn.microsoft.com/sysinternals/downloads/procdump)                        |
| LaZagne.exe                     | Pull stored credentials on Windows                               | [https://github.com/AlessandroZ/LaZagne/releases](https://github.com/AlessandroZ/LaZagne/releases)                                                               |
| PowerHuntShares                 | Hunt SMB shares for credentials at scale                         | [https://github.com/NetSPI/PowerHuntShares](https://github.com/NetSPI/PowerHuntShares)                                                                           |
| dnSpy                           | Inspect .NET DLLs for connection strings/creds                   | [https://github.com/dnSpyEx/dnSpy/releases](https://github.com/dnSpyEx/dnSpy/releases)                                                                           |
| PSUpload.ps1                    | PowerShell upload helper (WebClient.UploadFile)                  | [https://github.com/juliourena/plaintext/blob/master/Powershell/PSUpload.ps1](https://github.com/juliourena/plaintext/blob/master/Powershell/PSUpload.ps1)       |


## 2. AD Tools — Do NOT Require Transfer (run from Linux attack host)

|Tool|Purpose|Acquisition|
|---|---|---|
|Responder|LLMNR/NBT-NS/mDNS poisoning; fake SMB/HTTP/SQL; hash capture|[https://github.com/lgandx/Responder](https://github.com/lgandx/Responder)|
|Kerbrute (linux_amd64)|AD username enum + password spray via Kerberos preauth|[https://github.com/ropnop/kerbrute/releases](https://github.com/ropnop/kerbrute/releases)|
|windapsearch|LDAP-based AD user/group/privileged-user enumeration|[https://github.com/ropnop/windapsearch](https://github.com/ropnop/windapsearch)|
|bloodhound-python|BloodHound collector from Linux (use w/ creds, no RDP)|[https://github.com/fox-it/BloodHound.py](https://github.com/fox-it/BloodHound.py)|
|BloodHound (GUI)|Analyse AD attack paths / custom cypher queries|[https://github.com/BloodHoundAD/BloodHound](https://github.com/BloodHoundAD/BloodHound)|
|ldapsearch|Query password policy / check anon LDAP bind|`apt install ldap-utils` (openldap clients)|
|NetExec (nxc)|SMB/LDAP/SSH/etc enum, spraying, NTDS/SAM/LSA dump, exec|[https://github.com/Pennyw0rth/NetExec](https://github.com/Pennyw0rth/NetExec) (formerly CME)|
|Evil-WinRM|WinRM shell; PTH supported; upload/download|[https://github.com/Hackplayers/evil-winrm](https://github.com/Hackplayers/evil-winrm)|
|impacket-secretsdump (secretsdump.py)|Dump NTDS/SAM/LSA hashes (DCSync `-just-dc`, LOCAL)|[https://github.com/fortra/impacket](https://github.com/fortra/impacket)|
|impacket-getuserspns (GetUserSPNs.py)|Kerberoast / ASREPRoast from Linux|[https://github.com/fortra/impacket](https://github.com/fortra/impacket)|
|impacket-psexec|PTH → interactive shell (SMB)|[https://github.com/fortra/impacket](https://github.com/fortra/impacket)|
|impacket-wmiexec (wmiexec.py)|PTH command exec via WMI (135)|[https://github.com/fortra/impacket](https://github.com/fortra/impacket)|
|impacket-atexec|PTH command exec via atsvc/scheduler|[https://github.com/fortra/impacket](https://github.com/fortra/impacket)|
|impacket-smbexec|PTH command exec via SMB|[https://github.com/fortra/impacket](https://github.com/fortra/impacket)|
|impacket-smbclient (smbclient.py)|SMB shell; PTH supported|[https://github.com/fortra/impacket](https://github.com/fortra/impacket)|
|impacket-ntlmrelayx|NTLM relay (SMB/HTTP)|[https://github.com/fortra/impacket](https://github.com/fortra/impacket)|
|impacket-ticketer (ticketer.py)|Forge golden/extra-sid TGT ccache|[https://github.com/fortra/impacket](https://github.com/fortra/impacket)|
|impacket-raisechild|Automated child→parent ExtraSids escalation|[https://github.com/fortra/impacket](https://github.com/fortra/impacket)|
|impacket-lookupsid (lookupsid.py)|SID brute → domain SID / EA SID|[https://github.com/fortra/impacket](https://github.com/fortra/impacket)|
|impacket-samrdump (samrdump.py)|SAMR user/SID enumeration|[https://github.com/fortra/impacket](https://github.com/fortra/impacket)|
|impacket-mssqlclient (mssqlclient.py)|MSSQL login + xp_cmdshell/impersonation/linked|[https://github.com/fortra/impacket](https://github.com/fortra/impacket)|
|KeyTabExtract (keytabextract.py)|Extract hashes from keytab files (Linux domain-joined)|[https://github.com/sosdave/KeyTabExtract](https://github.com/sosdave/KeyTabExtract)|

---

## 3. Other Tools — Require Transfer to Host

### 3a. To Windows host

| Tool                                                                                                                                                                                                             | Purpose                                                | Acquisition                                                                                                                                                               |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| (See §1 for WinPEAS, SharpUp, LaZagne, PowerHuntShares, PSUpload, accesschk/pipelist/PsService/procdump, SeBackupPrivilege, EnableAllTokenPrivs, psgetsystem, dnSpy — all also used for Windows PE / pillaging.) |                                                        |                                                                                                                                                                           |
| ligolo-ng (agent.exe)                                                                                                                                                                                            | Pivoting agent on Windows victim                       | [https://github.com/nicocha30/ligolo-ng/releases](https://github.com/nicocha30/ligolo-ng/releases)                                                                        |
| chisel (chisel.exe)                                                                                                                                                                                              | SOCKS5 pivot (backup if ligolo fails)                  | [https://github.com/jpillora/chisel/releases](https://github.com/jpillora/chisel/releases)                                                                                |
| JuicyPotato                                                                                                                                                                                                      | SeImpersonate → SYSTEM (pre-Server 2019)               | [https://github.com/ohpe/juicy-potato/releases](https://github.com/ohpe/juicy-potato/releases)                                                                            |
| PrintSpoofer / RoguePotato                                                                                                                                                                                       | SeImpersonate → SYSTEM (newer builds)                  | [https://github.com/itm4n/PrintSpoofer](https://github.com/itm4n/PrintSpoofer) / [https://github.com/antonioCoco/RoguePotato](https://github.com/antonioCoco/RoguePotato) |
| nc.exe                                                                                                                                                                                                           | netcat for Windows (reverse shells / binpath payloads) | [https://github.com/int0x33/nc.exe/](https://github.com/int0x33/nc.exe/) (or seclists)                                                                                    |


### 3b. To Linux host

|Tool|Purpose|Acquisition|
|---|---|---|
|linpeas.sh|Automated Linux PE enumeration|[https://github.com/peass-ng/PEASS-ng/releases](https://github.com/peass-ng/PEASS-ng/releases) (linPEAS)|
|pspy|Enumerate running cronjobs/processes without root|[https://github.com/DominicBreuker/pspy/releases](https://github.com/DominicBreuker/pspy/releases)|
|ligolo-ng (agent)|Pivoting agent on Linux victim|[https://github.com/nicocha30/ligolo-ng/releases](https://github.com/nicocha30/ligolo-ng/releases)|
|chisel|SOCKS5 pivot (Linux build)|[https://github.com/jpillora/chisel/releases](https://github.com/jpillora/chisel/releases)|
|LaZagne (laZagne.py)|Pull stored creds (python2.7, needs admin)|[https://github.com/AlessandroZ/LaZagne](https://github.com/AlessandroZ/LaZagne)|
|minipenguin.py|Dump Linux user passwords (admin req)|[https://github.com/huntergregal/minipenguin](https://github.com/huntergregal/minipenguin)|
|shell.c / root.so (gcc)|LD_PRELOAD / shared-object hijack / NFS-setuid payloads|compiled locally with `gcc` (no external dl)|
|root.sh (tar wildcard)|Cron wildcard abuse payload (echo-generated)|inline `echo` payload — no download|

---

## 4. Other Tools — Do NOT Require Transfer (run from Linux attack host)

### 4a. Recon / discovery / web

|Tool|Purpose|Acquisition|
|---|---|---|
|nmap|Port/service/script scan; ping sweep; FTP bounce; mssql/mysql/rdp scripts|[https://nmap.org/](https://nmap.org/) (`apt install nmap`)|
|gobuster|dir / vhost fuzzing|[https://github.com/OJ/gobuster](https://github.com/OJ/gobuster)|
|ffuf|dir/page/vhost/param/value/subdomain fuzzing|[https://github.com/ffuf/ffuf](https://github.com/ffuf/ffuf)|
|seclists (wordlists)|web-extensions, subdomains-top1million, burp-parameter-names, etc.|[https://github.com/danielmiessler/SecLists](https://github.com/danielmiessler/SecLists)|
|wpscan|WordPress enum + bruteforce + vuln (api-token)|[https://github.com/wpscanteam/wpscan](https://github.com/wpscanteam/wpscan)|
|droopescan|Drupal/Joomla enum|[https://github.com/SamJoan/droopescan](https://github.com/SamJoan/droopescan)|
|eyewitness|Web screenshot / tomcat discovery|[https://github.com/FortyNorthSecurity/EyeWitness](https://github.com/FortyNorthSecurity/EyeWitness)|
|wafw00f|WAF detection|[https://github.com/EnableSecurity/wafw00f](https://github.com/EnableSecurity/wafw00f)|
|whois|Domain WHOIS|`apt install whois`|
|curl / jq (crt.sh)|CT log subdomain enum|`apt install curl jq`|
|sqlmap|Automated SQLi (all DBMS, all injection types)|[https://github.com/sqlmapproject/sqlmap](https://github.com/sqlmapproject/sqlmap)|
|XSStrike|Automated XSS discovery|[https://github.com/s0md3v/XSStrike](https://github.com/s0md3v/XSStrike)|
|XXEinjector|Automated OOB XXE exfil|[https://github.com/enjoiz/XXEinjector](https://github.com/enjoiz/XXEinjector)|
|IIS-ShortName-Scanner|IIS 8.3 short-name enumeration|[https://github.com/irsdl/IIS-ShortName-Scanner](https://github.com/irsdl/IIS-ShortName-Scanner) (needs java)|
|Burp Suite (CE)|Proxy / Repeater / Intruder / Decoder / match&replace|[https://portswigger.net/burp/releases](https://portswigger.net/burp/releases)|
|ZAP Scanner|Active web scan (CE Burp lacks one)|[https://www.zaproxy.org/](https://www.zaproxy.org/)|
|Wappalyzer|Tech-stack fingerprint|[https://www.wappalyzer.com/](https://www.wappalyzer.com/) (browser ext)|
|PayloadsAllTheThings|XSS/Upload/XXE payload refs|[https://github.com/swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)|
|cookieextractor.py|Extract Firefox cookies from cookies.sqlite|[https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/cookieextractor.py](https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/cookieextractor.py)|
|gitlab_13_10_2_rce.py|GitLab ≤13.10.2 auth RCE|ExploitDB 49951 — [https://www.exploit-db.com/exploits/49951](https://www.exploit-db.com/exploits/49951)|
|searchsploit|ExploitDB local search (ColdFusion/Druva/etc.)|`apt install exploitdb`|

### 4b. Service / protocol enumeration & attack

|Tool|Purpose|Acquisition|
|---|---|---|
|enum4linux / enum4linux-ng|SMB/rpcclient/LDAP auto-enum; password policy anon|[https://github.com/cddmp/enum4linux-ng](https://github.com/cddmp/enum4linux-ng)|
|smbmap|List/browse/read/upload SMB shares|[https://github.com/ShawnDEvans/smbmap](https://github.com/ShawnDEvans/smbmap)|
|smbclient|List/access SMB shares|`apt install smbclient` (samba)|
|rpcclient|Null session; enumdomusers/srvinfo/querydominfo|`apt install samba`|
|dig|DNS SOA/NS/AXFR/any/CH version|`apt install dnsutils`|
|dnsenum|Subdomain bruteforce + zone xfer|`apt install dnsenum`|
|subfinder|Passive subdomain enum|[https://github.com/projectdiscovery/subfinder](https://github.com/projectdiscovery/subfinder)|
|fierce|DNS enum + zone xfer|[https://github.com/mschwager/fierce](https://github.com/mschwager/fierce)|
|smtp-user-enum|SMTP VRFY/EXPN/RCPT user enum|[https://github.com/pentestmonkey/smtp-user-enum](https://github.com/pentestmonkey/smtp-user-enum)|
|o365spray|O365 validate/enum/spray|[https://github.com/0xZDH/o365spray](https://github.com/0xZDH/o365spray)|
|telnet / openssl s_client|SMTP/IMAP/POP3 raw interaction|`apt install telnet openssl`|
|onesixtyone|SNMP community-string bruteforce|[https://github.com/trailofbits/onesixtyone](https://github.com/trailofbits/onesixtyone)|
|snmpwalk|SNMP OID walk|`apt install snmp` (net-snmp)|
|braa|Mass SNMP OID bruteforce|[https://github.com/mteg/braa](https://github.com/mteg/braa)|
|showmount / mount -t nfs|NFS share enum + mount|`apt install nfs-common`|
|mysql (client)|MySQL login + SELECT/OUTFILE/LOAD_FILE|`apt install default-mysql-client`|
|sqsh|MSSQL client (alt to impacket)|`apt install sqsh`|
|ODAT|Oracle TNS auto-scan + utlfile upload|[https://github.com/quentinhardy/odat](https://github.com/quentinhardy/odat)|
|sqlplus|Oracle TNS login|Oracle Instant Client ([https://www.oracle.com/…](https://www.oracle.com/%E2%80%A6))|
|xfreerdp / rdesktop / remmina|RDP session (incl. PTH via xfreerdp /pth:)|`apt install freerdp2-x11 rdesktop remmina`|
|rdp-sec-check|RDP security audit|[https://github.com/CiscoCXSecurity/rdp-sec-check](https://github.com/CiscoCXSecurity/rdp-sec-check)|
|hydra|Multi-protocol bruteforce/spray|[https://github.com/vanhauser-thc/thc-hydra](https://github.com/vanhauser-thc/thc-hydra)|
|medusa|Multi-protocol bruteforce (FTP/etc.)|[https://github.com/jmk-foofus/medusa](https://github.com/jmk-foofus/medusa)|
|Metasploit (msfconsole/msfvenom)|smb_login, mssql_ping, ipmi_*, smb_delivery, tomcat_mgr_login, jenkins_script_console, wp_admin_shell_upload, multi/handler|[https://github.com/rapid7/metasploit-framework](https://github.com/rapid7/metasploit-framework)|
|kubeletctl|K8s pod enum/RCE/token+cert extraction|[https://github.com/cyberark/KubeletCTL](https://github.com/cyberark/KubeletCTL)|
|kubectl|K8s API auth can-i / apply privesc pod|[https://github.com/kubernetes/kubectl](https://github.com/kubernetes/kubectl)|

### 4c. Hash cracking / post-processing

|Tool|Purpose|Acquisition|
|---|---|---|
|hashcat|Crack NTLM(1000)/NTLMv2(5600)/Kerberoast(13100,19600,19700)/sha512crypt(1800)/DCC2(2100)/IPMI(7300)|[https://github.com/hashcat/hashcat](https://github.com/hashcat/hashcat)|
|JohnTheRipper|Wordlist/incremental; _2john_ helpers|[https://github.com/openwall/john](https://github.com/openwall/john)|
|hashid|Identify hash type (-m hashcat / -j john)|[https://github.com/psypanda/hashID](https://github.com/psypanda/hashID)|
|unshadow|Combine passwd+shadow for cracking|ships with John|
|pypykatz|Parse LSASS minidump offline (Linux)|[https://github.com/skelsec/pypykatz](https://github.com/skelsec/pypykatz)|
|guestmount|Mount VMDK/VHDX offline for secretsdump|`apt install libguestfs-tools`|
|xsltproc|Convert nmap XML → HTML|`apt install xsltproc`|

### 4d. File-transfer servers & pivoting (attacker side)

|Tool|Purpose|Acquisition|
|---|---|---|
|goshs|HTTP/HTTPS up-download server (preferred)|[https://github.com/patrickhener/goshs](https://github.com/patrickhener/goshs)|
|python3 -m http.server|HTTP download (fallback)|stdlib|
|python3 -m uploadserver|HTTP upload (fallback)|`pip install uploadserver`|
|pyftpdlib|FTP server (py)|`pip install pyftpdlib`|
|impacket-smbserver|SMB share (anon or user/pass)|[https://github.com/fortra/impacket](https://github.com/fortra/impacket)|
|wsgidav|WebDAV server (anon)|`pip install wsgidav`|
|ligolo-ng (proxy)|Pivoting proxy on attacker|[https://github.com/nicocha30/ligolo-ng/releases](https://github.com/nicocha30/ligolo-ng/releases)|
|chisel (server)|SOCKS5 server|[https://github.com/jpillora/chisel/releases](https://github.com/jpillora/chisel/releases)|
|proxychains|Route tools over socks5|`apt install proxychains4`|
|tcpdump / Wireshark|Passive network enum|`apt install tcpdump wireshark`|
|username-anarchy|Generate possible usernames|[https://github.com/urbanadventurer/username-anarchy](https://github.com/urbanadventurer/username-anarchy)|