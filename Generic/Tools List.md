### AD Tools that require transfer to victim

- **PowerView.ps1** — PowerShell module for AD enumeration (users, groups, ACLs, SPNs, trusts, etc.). Acquire: [https://github.com/PowerShellMafia/PowerSploit](https://github.com/PowerShellMafia/PowerSploit) (PowerView.ps1) or [https://github.com/ZeroDayLab/PowerSploit](https://github.com/ZeroDayLab/PowerSploit)
- **SharpView.exe** — C# port of PowerView for AD enumeration. Acquire: [https://github.com/tevora-threat/SharpView](https://github.com/tevora-threat/SharpView)
- **SharpHound.exe** — BloodHound data collector (ingest into BloodHound GUI). Acquire: [https://github.com/BloodHoundAD/SharpHound](https://github.com/BloodHoundAD/SharpHound)
- **Snaffler.exe** — Finds interesting files/credentials on shares. Acquire: [https://github.com/SnaffCon/Snaffler](https://github.com/SnaffCon/Snaffler)
- **Rubeus.exe** — Kerberos abuse toolkit (Kerberoast, ASREPRoast, ticket manipulation, etc.). Acquire: [https://github.com/GhostPack/Rubeus](https://github.com/GhostPack/Rubeus)
- **mimikatz.exe** — Credential dumping, DCSync, PTH, ticket ops. Acquire: [https://github.com/gentilkiwi/mimikatz](https://github.com/gentilkiwi/mimikatz)
- **PowerUpSQL.ps1** — Discover and attack SQL Server instances in the domain. Acquire: [https://github.com/NetSPI/PowerUpSQL](https://github.com/NetSPI/PowerUpSQL)
- **DomainPasswordSpray.ps1** — Domain password spraying from a Windows host. Acquire: [https://github.com/dafthack/DomainPasswordSpray](https://github.com/dafthack/DomainPasswordSpray)
- **Invoke-TheHash** (Invoke-SMBExec / Invoke-WMIExec etc.) — PTH command execution. Acquire: [https://github.com/Kevin-Robertson/Invoke-TheHash](https://github.com/Kevin-Robertson/Invoke-TheHash)

### AD Tools that do not require transfer to victim

- **Responder** — LLMNR/NBT-NS/mDNS poisoning + credential capture. Acquire: [https://github.com/lgandx/Responder](https://github.com/lgandx/Responder) (or apt install responder)
- **kerbrute** — Username enumeration & password spraying via Kerberos. Acquire: [https://github.com/ropnop/kerbrute](https://github.com/ropnop/kerbrute)
- **BloodHound.py / bloodhound-python** — Python BloodHound collector (no Windows foothold needed). Acquire: [https://github.com/fox-it/BloodHound.py](https://github.com/fox-it/BloodHound.py)
- **ldapsearch** — LDAP queries (password policy, anonymous bind, etc.). Built-in on most Linux distros / apt install ldap-utils
- **Windapsearch** — LDAP enumeration helper. Acquire: [https://github.com/ropnop/windapsearch](https://github.com/ropnop/windapsearch)
- **Impacket suite** (GetUserSPNs.py, GetNPUsers.py, secretsdump.py, psexec.py, wmiexec.py, atexec.py, smbexec.py, smbclient.py, etc.) — Kerberoasting, ASREPRoasting, DCSync, PTH shells, SMB, etc. Acquire: [https://github.com/fortra/impacket](https://github.com/fortra/impacket) (pip install impacket or apt install python3-impacket)
- **NetExec / nxc** (formerly CrackMapExec) — SMB/WinRM/LDAP enumeration, spraying, PTH, command execution. Acquire: [https://github.com/Pennyw0rth/NetExec](https://github.com/Pennyw0rth/NetExec)
- **enum4linux / enum4linux-ng** — SMB/LDAP/RID enumeration. Acquire: [https://github.com/cddmp/enum4linux-ng](https://github.com/cddmp/enum4linux-ng)
- **rpcclient / smbclient / smbmap** — SMB/RPC enumeration and interaction. Built-in / apt install smbclient smbmap
- **wireshark / tcpdump** — Network traffic capture for LLMNR etc. Built-in / apt install wireshark tcpdump

### Other tools that require transfer to victim

- **LinPEAS.sh** — Automated Linux privilege-escalation enumeration. Acquire: [https://github.com/peass-ng/PEASS-ng](https://github.com/peass-ng/PEASS-ng)
- **WinPEAS.exe** — Automated Windows privilege-escalation enumeration. Acquire: [https://github.com/peass-ng/PEASS-ng](https://github.com/peass-ng/PEASS-ng)
- **ligolo-ng agent** — Pivot / tunnel client. Acquire: [https://github.com/nicocha30/ligolo-ng](https://github.com/nicocha30/ligolo-ng)
- **Chisel client** — Alternative pivot / SOCKS tunnel client. Acquire: [https://github.com/jpillora/chisel](https://github.com/jpillora/chisel)
- **PSUpload.ps1** — PowerShell file upload helper. (Mentioned in ToolkitLibrary; acquire from common PowerShell transfer snippets or recreate)
- **msfvenom-generated payloads** (.exe, .elf, .msi, .war, etc.) — Reverse shells / staged payloads. Generated on attacker with Metasploit Framework
- **dnSpy** — .NET / DLL decompilation & inspection. Acquire: [https://github.com/dnSpy/dnSpy](https://github.com/dnSpy/dnSpy)

### Other tools that do not require transfer to victim

- **ffuf** — Web content / vhost / parameter fuzzing. Acquire: [https://github.com/ffuf/ffuf](https://github.com/ffuf/ffuf)
- **sqlmap** — Automated SQL injection. Acquire: [https://github.com/sqlmapproject/sqlmap](https://github.com/sqlmapproject/sqlmap) (apt install sqlmap)
- **Burp Suite** — Web proxy, scanning, manual testing. Acquire: [https://portswigger.net/burp](https://portswigger.net/burp)
- **nmap** — Port / service / script scanning. Built-in / apt install nmap
- **msfvenom / Metasploit Framework** (multi/handler, smb_delivery, etc.) — Payload generation & listeners. Acquire: [https://www.metasploit.com](https://www.metasploit.com) / apt install metasploit-framework
- **nc / netcat / ncat** — Listeners & basic shells. Built-in
- **evil-winrm** — WinRM shells (including PTH). Acquire: [https://github.com/Hackplayers/evil-winrm](https://github.com/Hackplayers/evil-winrm)
- **xfreerdp** — RDP client (supports PTH / Restricted Admin). Built-in / apt install freerdp2-x11
- **hashcat / John the Ripper** — Offline hash cracking. Acquire: [https://hashcat.net](https://hashcat.net) / [https://www.openwall.com/john/](https://www.openwall.com/john/) (apt install hashcat john)
- **hashid** — Hash type identification. Acquire: [https://github.com/psypanda/hashID](https://github.com/psypanda/hashID)
- **ligolo-ng proxy** / **Chisel server** — Pivot servers (run on attacker). Same GitHub repos as agents above
- **proxychains** — Route tools through SOCKS tunnels. Built-in / apt install proxychains4