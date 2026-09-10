# CPTS Tool, Script, Executable & Payload Inventory

> Scope: derived from the main `CPTS_Notes` tree only. `Extras/` is intentionally excluded. The existing `Checklist(SOP)` material is not used as the authority for this inventory.

## How to use this file

- **Require transfer to host** means the artifact is normally copied/executed on the compromised target or pivot host (PowerShell script, EXE, shell script, agent, etc.).
- **Does not require transfer to host** means the operator can normally run the tool from the attack box or use a native/remote protocol without copying a tool to the target.
- Some tools can technically be used either way. They are classified according to the workflow emphasized in the notes.
- URLs are acquisition/reference links; installation and version selection are intentionally left to the operator.

---

## 1. Active Directory — Requires transfer to host

| Tool / artifact | What it is used for | Typical outcome | Acquire / reference |
|---|---|---|---|
| **PowerView.ps1** | PowerShell AD/domain/user/group/ACL/trust enumeration and abuse | Domain recon, ACL paths, SPNs, trusts, privileges | https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon |
| **SharpView.exe** | Compiled PowerView functionality | AD enumeration when PowerShell/module loading is inconvenient | https://github.com/tevora-threat/SharpView |
| **Snaffler.exe** | Searches shares/files for credentials and sensitive material | Passwords, keys, configs, secrets | https://github.com/SnaffCon/Snaffler |
| **SharpHound.exe** | BloodHound data collection from Windows | AD relationship/attack-path dataset | https://github.com/SpecterOps/SharpHound |
| **PowerUpSQL.ps1** | MSSQL/domain/SQL privilege enumeration and abuse | SQL credentials, server access, escalation/lateral movement | https://github.com/NetSPI/PowerUpSQL |
| **Rubeus.exe** | Kerberos enumeration and abuse | Kerberoast/AS-REP roast material, ticket operations, delegation/trust abuse | https://github.com/GhostPack/Rubeus |
| **mimikatz.exe** | Windows credential and ticket operations; DCSync/golden-ticket related work | NTLM hashes, tickets, domain secrets, impersonation material | https://github.com/gentilkiwi/mimikatz |
| **Invoke-TheHash.psd1** | Pass-the-Hash SMB/WMI functions | Remote execution/auth using NTLM material | https://github.com/Kevin-Robertson/Invoke-TheHash |
| **DomainPasswordSpray.ps1** | Domain password spraying from Windows | Additional valid accounts | https://github.com/dafthack/DomainPasswordSpray |
| **DSInternals.psd1** | NTDS/AD database analysis and password material extraction | NT hashes and AD secret material | https://github.com/MichaelGrafnetter/DSInternals |
| **SeBackupPrivilege-related utilities** | Abuse Backup Operator / SeBackupPrivilege workflow | Read protected files/NTDS material | See Windows privilege-escalation notes; use the repo's referenced tooling |
| **EnableAllTokenPrivs** | Enables assigned Windows token privileges | Makes otherwise-disabled privileges usable for an escalation path | https://github.com/hatRiot/EnableAllTokenPrivs |
| **psgetsystem** | Uses token/privilege techniques to obtain SYSTEM | SYSTEM shell/token | https://github.com/decoder-it/psgetsystem |
| **SharpUp.exe** | Windows local privilege-escalation enumeration | High-signal LPE findings | https://github.com/GhostPack/SharpUp |
| **WinPEAS.exe** | Windows privilege-escalation enumeration | Broad LPE findings and prioritization hints | https://github.com/peass-ng/PEASS-ng |
| **accesschk.exe / accesschk64.exe** | Check object/file/service permissions | Identifies weak permissions exploitable for LPE | https://learn.microsoft.com/sysinternals/downloads/accesschk |
| **pipelist.exe** | Named-pipe enumeration | Identifies interesting/weak named pipes | https://learn.microsoft.com/sysinternals/downloads/pipelist |
| **PsService.exe** | Service enumeration/control | Service misconfiguration discovery / service abuse | https://learn.microsoft.com/sysinternals/downloads/psservice |
| **procdump.exe** | Process dump collection | Credential material from dumpable processes | https://learn.microsoft.com/sysinternals/downloads/procdump |
| **LaZagne.exe** | Credential hunting from local applications | Recovered saved credentials | https://github.com/AlessandroZ/LaZagne |
| **PowerHuntShares** | Find sensitive material in Windows shares | Credentials/secrets in files/shares | https://github.com/V3rd0n/PowerHuntShares |
| **dnSpy** | Inspect .NET assemblies | Hard-coded credentials, connection strings, hidden logic | https://github.com/dnSpyEx/dnSpy |

---

## 2. Active Directory — Does not require transfer to host

| Tool | What it is used for | Typical outcome | Acquire / reference |
|---|---|---|---|
| **Responder** | LLMNR/NBT-NS/mDNS poisoning and credential capture | NTLMv2 challenge/response capture | https://github.com/lgandx/Responder |
| **Kerbrute** | Kerberos user enumeration and password-spray workflows | Valid usernames; authentication results | https://github.com/ropnop/kerbrute |
| **windapsearch** | LDAP-based AD enumeration | Users, groups, computers, policy and directory data | https://github.com/ropnop/windapsearch |
| **bloodhound-python** | BloodHound data collection from Linux | AD relationship/attack-path dataset | https://github.com/fox-it/BloodHound.py |
| **BloodHound** | Visualize and query AD relationships | Attack paths / privilege chains | https://github.com/SpecterOps/BloodHound |
| **ldapsearch** | LDAP directory queries | Direct directory enumeration | https://www.openldap.org/software/ |
| **NetExec (nxc)** | SMB/WinRM/LDAP enumeration, password testing, remote execution, share spidering | Credentials, shares, sessions, remote access | https://github.com/Pennyw0rth/NetExec |
| **Evil-WinRM** | WinRM remote shell | Windows foothold / lateral movement | https://github.com/Hackplayers/evil-winrm |
| **impacket-secretsdump** | Remote/local SAM/LSA/NTDS extraction and DCSync-related collection | NTLM hashes and other credential material | https://github.com/fortra/impacket |
| **impacket-getuserspns** | Enumerate SPN accounts and request TGS material | Kerberoastable hashes | https://github.com/fortra/impacket |
| **impacket-psexec** | SMB-based remote execution | Shell / command execution as target account | https://github.com/fortra/impacket |
| **impacket-wmiexec** | WMI remote execution | Remote shell/command execution | https://github.com/fortra/impacket |
| **impacket-atexec** | Task Scheduler remote execution | Remote command execution | https://github.com/fortra/impacket |
| **impacket-smbexec** | SMB service-based remote execution | Remote command execution | https://github.com/fortra/impacket |
| **impacket-smbclient** | SMB share browsing/interaction | Files, credentials, writable paths | https://github.com/fortra/impacket |
| **impacket-ntlmrelayx** | NTLM relay | Relay captured authentication into usable access | https://github.com/fortra/impacket |
| **impacket-ticketer** | Kerberos ticket creation | Forged/usable tickets when prerequisites are met | https://github.com/fortra/impacket |
| **impacket-raisechild** | Child-domain trust escalation workflow | Higher-level domain access when trust conditions are met | https://github.com/fortra/impacket |
| **impacket-lookupsid** | SID/RID enumeration | Domain account/group discovery | https://github.com/fortra/impacket |
| **impacket-samrdump** | SAMR-based account/domain enumeration | Users/groups/domain information | https://github.com/fortra/impacket |
| **impacket-mssqlclient** | MSSQL authentication and interaction | Database access, SQL-level execution paths | https://github.com/fortra/impacket |
| **KeyTabExtract** | Extract keys from Kerberos keytab files | Kerberos keys for authentication/cracking | https://github.com/sosdave/KeyTabExtract |
| **bloodyAD** | LDAP/AD object manipulation | Password reset, group modification, SPN/gMSA operations, ACL abuse | https://github.com/CravateRouge/bloodyAD |

---

## 3. Other — Requires transfer to host

| Tool / artifact | What it is used for | Typical outcome | Acquire / reference |
|---|---|---|---|
| **ligolo-ng agent** | Pivot/tunnel agent placed on compromised host | Network-level access to otherwise unreachable segments | https://github.com/nicocha30/ligolo-ng |
| **chisel client/agent** | TCP tunnelling from a compromised host | Port forwarding/pivoting | https://github.com/jpillora/chisel |
| **JuicyPotato** | Windows token/COM privilege escalation on suitable systems | SYSTEM | https://github.com/ohpe/juicy-potato |
| **PrintSpoofer** | Abuse SeImpersonatePrivilege via Print Spooler behavior | SYSTEM | https://github.com/itm4n/PrintSpoofer |
| **RoguePotato** | Alternate SeImpersonatePrivilege escalation path | SYSTEM | https://github.com/antonioCoco/RoguePotato |
| **nc.exe** | Windows netcat listener/client | Reverse shell, bind shell, simple file movement | https://github.com/int0x33/nc.exe |
| **linpeas.sh** | Linux local privilege-escalation enumeration | Broad LPE findings | https://github.com/peass-ng/PEASS-ng |
| **pspy** | Linux process/cron monitoring without root | Discover scheduled commands/processes and secrets | https://github.com/DominicBreuker/pspy |
| **LaZagne.py** | Local credential hunting from Linux applications | Recovered saved credentials | https://github.com/AlessandroZ/LaZagne |
| **minipenguin.py** | Linux credential / environment enumeration referenced in the notes | Local credentials/session material | Use the project/source referenced by the note |

---

## 4. Other — Does not require transfer to host

### Recon / web / network

| Tool | Primary use | Typical outcome | Acquire / reference |
|---|---|---|---|
| **nmap** | Port/service/version enumeration | Attack-surface map | https://nmap.org/download.html |
| **gobuster** | Directory/DNS/vhost enumeration | Hidden paths, hosts, endpoints | https://github.com/OJ/gobuster |
| **ffuf** | Web fuzzing and parameter/content discovery | Hidden endpoints, parameters, files | https://github.com/ffuf/ffuf |
| **SecLists** | Wordlists for directory, subdomain, parameter, credential testing | Better discovery coverage | https://github.com/danielmiessler/SecLists |
| **WPScan** | WordPress enumeration | Version/plugin/theme/user findings and vuln candidates | https://wpscan.com/ |
| **droopescan** | CMS enumeration | Drupal/Joomla/SilverStripe-style discovery | https://github.com/droope/droopescan |
| **EyeWitness** | Screenshot and web-service triage | Fast visual triage of many hosts | https://github.com/FortyNorthSecurity/EyeWitness |
| **wafw00f** | WAF fingerprinting | WAF identification / test-path hints | https://github.com/EnableSecurity/wafw00f |
| **whois** | Domain registration/ownership information | Scope/subdomain/context clues | https://man7.org/linux/man-pages/man1/whois.1.html |
| **curl** | HTTP/API interaction | Reproduce requests, test auth/headers/verbs | https://curl.se/download.html |
| **jq** | Parse JSON API output | Faster API enumeration/analysis | https://jqlang.org/download/ |
| **sqlmap** | Automated SQL injection testing/exploitation | DB enumeration, data access, sometimes OS-level escalation | https://sqlmap.org/ |
| **XSStrike** | XSS discovery/validation | Reflected/stored/DOM XSS candidates | https://github.com/s0md3v/XSStrike |
| **XXEinjector** | XXE testing | File read / SSRF-style XXE impact | https://github.com/enjoiz/XXEinjector |
| **IIS-ShortName-Scanner** | IIS 8.3 tilde short-name enumeration | Short file/folder name disclosure | https://github.com/irsdl/IIS-ShortName-Scanner |
| **Burp Suite Community Edition** | Intercept/replay/fuzz web requests | Auth/access-control/input-validation testing | https://portswigger.net/burp/communitydownload |
| **OWASP ZAP** | Web proxy/scanner | Automated and manual web testing | https://www.zaproxy.org/download/ |
| **Wappalyzer** | Web technology fingerprinting | Framework/server/CMS identification | https://www.wappalyzer.com/ |
| **PayloadsAllTheThings** | Payload/reference corpus | Payload selection by vulnerability class | https://github.com/swisskyrepo/PayloadsAllTheThings |
| **cookieextractor.py** | Extract/test application cookie material (as referenced in notes) | Session-cookie reuse/analysis | Use source referenced in the note/repo |
| **gitlab_13_10_2_rce.py** | Version-specific GitLab exploit script referenced by the notes | RCE against the matching vulnerable GitLab condition | ExploitDB 49951 / source linked by the note |
| **searchsploit** | Local Exploit-DB search | Match product/version to known exploit candidates | https://www.exploit-db.com/searchsploit |

### Enumeration / Windows services / file shares

| Tool | Primary use | Typical outcome | Acquire / reference |
|---|---|---|---|
| **enum4linux / enum4linux-ng** | SMB/RPC/domain enumeration | Users, groups, shares, policy, domain info | https://github.com/CiscoCXSecurity/enum4linux ; https://github.com/cddmp/enum4linux-ng |
| **smbmap** | SMB share and permission enumeration | Read/write shares, lateral movement opportunities | https://github.com/ShawnDEvans/smbmap |
| **smbclient** | SMB share access | File retrieval/upload/share interaction | https://www.samba.org/samba/docs/current/man-html/smbclient.1.html |
| **rpcclient** | MS-RPC/SAMR enumeration | Users, groups, domain info | https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html |
| **dig** | DNS queries | Records, nameservers, zone-transfer clues | https://bind9.readthedocs.io/ |
| **dnsenum** | DNS enumeration | Hosts, records, brute-force/zone information | https://github.com/fwaeytens/dnsenum |
| **subfinder** | Passive subdomain discovery | Subdomain inventory | https://github.com/projectdiscovery/subfinder |
| **fierce** | DNS/network reconnaissance | Subdomains, DNS/host relationships | https://github.com/mschwager/fierce |
| **smtp-user-enum** | SMTP user enumeration | Valid usernames | https://github.com/pentestmonkey/smtp-user-enum |
| **o365spray** | Microsoft 365/O365 username/password-spray checks | Valid accounts/authentication results | https://github.com/0xZDH/o365spray |

### Protocol / database / remote access

| Tool | Primary use | Typical outcome | Acquire / reference |
|---|---|---|---|
| **telnet** | Raw TCP service interaction | Banner/auth/service behavior testing | OS package / https://linux.die.net/man/1/telnet |
| **openssl s_client** | TLS service interaction | Certificate/service/auth inspection | https://docs.openssl.org/ |
| **onesixtyone** | SNMP community string scanning | SNMP-accessible hosts/communities | https://github.com/trailofbits/onesixtyone |
| **snmpwalk** | SNMP OID enumeration | Host/network/service information | https://www.net-snmp.org/ |
| **braa** | SNMP bulk querying | Fast SNMP information collection | https://github.com/mteg/braa |
| **showmount / mount -t nfs** | NFS export enumeration and mounting | File access; potential LPE via weak exports | OS utilities |
| **mysql client** | MySQL interaction | DB access, data and privilege testing | https://dev.mysql.com/downloads/ |
| **sqsh** | Sybase/MSSQL-style query client | MSSQL interaction and command execution paths | https://sqsh.sourceforge.net/ |
| **ODAT** | Oracle database assessment | Oracle enumeration/exploitation | https://github.com/quentinhardy/odat |
| **sqlplus** | Oracle SQL interaction | Database access and privilege testing | Oracle Instant Client / Oracle Database tooling |
| **xfreerdp / rdesktop / Remmina** | RDP client access | Interactive Windows session | https://www.freerdp.com/ ; https://www.rdesktop.org/ ; https://remmina.org/ |
| **rdp-sec-check** | RDP security configuration checks | RDP protocol/security weaknesses | https://github.com/CiscoCXSecurity/rdp-sec-check |
| **Hydra** | Network-service password testing | Valid service credentials | https://github.com/vanhauser-thc/thc-hydra |
| **Medusa** | Network-service password testing | Valid service credentials | https://github.com/jmk-foofus/medusa |
| **Metasploit Framework** | Exploitation, handlers, modules, payload generation | Shells/RCE/post-exploitation | https://github.com/rapid7/metasploit-framework |
| **Kubeletctl** | Kubelet API assessment | Pod/container access and cluster foothold where exposed | https://github.com/cyberark/kubeletctl |
| **kubectl** | Kubernetes API/client interaction | Cluster/container access | https://kubernetes.io/docs/tasks/tools/ |

### Password / hash handling

| Tool | Primary use | Typical outcome | Acquire / reference |
|---|---|---|---|
| **hashcat** | Offline password cracking | Recovered plaintext credentials | https://hashcat.net/hashcat/ |
| **John the Ripper** | Offline password cracking | Recovered plaintext credentials | https://www.openwall.com/john/ |
| **hashid** | Hash format identification | Choose correct cracking mode/tool | https://github.com/psypanda/hashID |
| **unshadow** | Combine passwd/shadow for John | Crackable Unix credential file | https://www.openwall.com/john/ |
| **pypykatz** | Parse Windows credential artifacts/dumps | Credential material from dumps | https://github.com/skelsec/pypykatz |

### File transfer / hosting / pivot support

| Tool | Primary use | Typical outcome | Acquire / reference |
|---|---|---|---|
| **guestmount** | Mount VM disk images | Offline file/credential access | https://libguestfs.org/guestmount.1.html |
| **xsltproc** | XSLT processing | XML/XXE-style processing support | https://gitlab.gnome.org/GNOME/libxslt |
| **goshs** | Simple authenticated HTTP file server | Transfer files through HTTP/HTTPS | https://github.com/patrickhener/goshs |
| **python3 -m http.server** | Simple HTTP file server | Transfer payloads/scripts | Python standard library |
| **python3 -m uploadserver** | HTTP upload server | Receive files from a target | https://github.com/Densifyd/uploadserver |
| **pyftpdlib** | Quick FTP server | FTP-based file transfer | https://github.com/giampaolo/pyftpdlib |
| **impacket-smbserver** | SMB file server from attacker host | Transfer scripts/EXEs over SMB | https://github.com/fortra/impacket |
| **wsgidav** | WebDAV server | HTTP/WebDAV file transfer | https://github.com/mar10/wsgidav |
| **ligolo-ng proxy** | Attacker-side endpoint for ligolo-ng | Pivot/tunnel termination | https://github.com/nicocha30/ligolo-ng |
| **chisel server** | Attacker-side endpoint for chisel | Reverse/forward tunnelling | https://github.com/jpillora/chisel |
| **proxychains** | Route applications through a proxy/pivot | Reach internal services through tunnel | https://github.com/haad/proxychains |
| **tcpdump / Wireshark** | Packet capture/analysis | Protocol identification, credential/traffic clues | https://www.tcpdump.org/ ; https://www.wireshark.org/ |
| **username-anarchy** | Username generation from names | Candidate username set for enumeration/spray | https://github.com/urbanadventurer/username-anarchy |

---

## 5. Payloads, scripts, and command artifacts explicitly represented in the notes

These are **not extra tools**; they are payload/workflow artifacts derived from the repo's `CommonPayload/` and attack notes.

| Artifact | Condition / use | Expected outcome |
|---|---|---|
| **Bash reverse shell** | Linux web/app or shell context with outbound connectivity | Callback shell |
| **PowerShell reverse shell** | Windows context with PowerShell and outbound connectivity | Callback shell |
| **msfvenom Linux x64 reverse shell payload** | Need a Linux executable callback | Remote shell after execution |
| **msfvenom Windows x86 reverse shell payload** | Need a Windows executable callback | Remote shell after execution |
| **msfvenom Windows/Linux payloads via `multi/handler`** | Generated payload is executed on target and listener is controlled by operator | Meterpreter/raw shell depending on selected payload |
| **Metasploit `windows/smb/smb_delivery` workflow** | Windows host can authenticate/connect to attacker | Payload retrieval/execution path |
| **PHP web shell one-liner** | PHP code execution is achieved in a web application | Web-command execution / shell pivot |
| **Python PTY shell upgrade** | Initial shell is a basic/non-interactive TTY | More usable interactive shell |
| **AlwaysInstallElevated MSI payload** | Both AlwaysInstallElevated policy conditions are present | SYSTEM-level execution |
| **Kerberoasting TGS request workflow** | Valid AD account + SPN-backed service accounts | Offline-crackable service-account material |
| **AS-REP roasting workflow** | Account permits pre-auth disabled | Offline-crackable AS-REP material |
| **Pass-the-Hash workflow** | Obtain NTLM hash + remote service accepting NTLM | Remote authentication without knowing plaintext |
| **DCSync workflow** | Account has required replication rights | Domain password hashes / secrets |
| **Golden-ticket workflow** | KRBTGT hash + domain/SID knowledge | Forged domain Kerberos TGT |
| **ExtraSIDs child-to-parent workflow** | Child domain compromise + trust conditions + KRBTGT material + no SID filtering | Parent/root-domain privileged access |

---

## 6. High-value classification shortcuts

- **Need a file/script/EXE on the target:** think PowerView, SharpView, SharpHound, Rubeus, mimikatz, PowerUpSQL, DomainPasswordSpray, WinPEAS, SharpUp, LaZagne, linpeas, pspy, JuicyPotato/PrintSpoofer/RoguePotato, tunnel agents.
- **Can stay on the attack box:** think nmap, NetExec, Impacket, BloodHound/bloodhound-python, Responder, Kerbrute, ldapsearch, SMB/RPC clients, web scanners/proxies, password crackers, database clients, remote shells.
- **Need a callback shell:** use one of the note's reverse-shell payload patterns plus the corresponding listener/handler.
- **Have hashes but no plaintext:** hashcat / John.
- **Have a web foothold:** use the note's PHP/Python shell and file-transfer methods before moving on.

## Source

Primary source: [TamHoTsun-Alvin/CPTS_Notes](https://github.com/TamHoTsun-Alvin/CPTS_Notes). Relevant main-note folders include `ActiveDirectory/`, `CommonPayload/`, `FileTransfer/`, `Generic/`, `Privilege Escalation/`, `Service Enumeration and Attack/`, and `WebAttacks/`. `Extras/` is deliberately excluded.
