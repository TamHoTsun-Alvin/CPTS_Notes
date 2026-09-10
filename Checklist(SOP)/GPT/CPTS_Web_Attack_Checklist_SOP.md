# CPTS — Web Attack Checklist & SOP

> Objective: fingerprint first, map attack surface second, test **condition → vulnerability → impact** in a consistent order. Includes generic web attacks and the specific web-facing applications represented in the main notes. `Extras/` is excluded.

---

# 1. Web attack triage

## Fingerprint

- [ ] Identify web server, framework, CMS, language and major components.
- [ ] Check HTTP/HTTPS certificates, redirects, headers, cookies and obvious product names.
- [ ] Look for version strings, changelogs and default pages.

### Tools / outcomes
- `nmap` → ports/services/version clues.
- `curl` → exact response/header/request behavior.
- `Wappalyzer` → technology fingerprint.
- `EyeWitness` → visual triage of many endpoints.
- `wafw00f` → WAF identification.

---

# 2. Content and endpoint discovery

- [ ] Enumerate directories/files.
- [ ] Enumerate virtual hosts/subdomains where relevant.
- [ ] Fuzz common extensions and application-specific paths.
- [ ] Discover parameters and alternate request methods.

### Condition → attack
- **Hidden admin/API/backup/debug path found** → direct endpoint testing.
- **Interesting parameter discovered** → input-validation branches (SQLi, XSS, command injection, LFI, etc.).
- **Virtual host discovered** → fingerprint and test separately.

### Tools
- `gobuster`
- `ffuf`
- `SecLists`
- `subfinder`
- `fierce`
- `Burp Suite Community Edition`
- `OWASP ZAP`

---

# 3. Authentication / authorization / access control

## Check
- [ ] Login behavior, account recovery, session cookies.
- [ ] Test alternate accounts/roles where authorized in the lab.
- [ ] Test object IDs and role-restricted endpoints.
- [ ] Test HTTP verbs on protected endpoints.

### Condition → attack
- **User can change object identifier and read another user's object** → IDOR.
- **Low-privilege user can reach admin-only endpoint** → broken access control.
- **Endpoint behaves differently under GET/POST/PUT/DELETE-style verbs** → HTTP Verb Tampering.
- **Weak/default credentials on a web-facing product** → authenticated attack surface.

### Tools / outcomes
- `Burp Suite`
- `curl`
- `OWASP ZAP`
- `Hydra` / `Medusa` where the application's login protocol is appropriate and the notes use them.

---

# 4. SQL injection branch

### Check
- [ ] Numeric/string parameters.
- [ ] Search, sort, filter, login and API parameters.
- [ ] Error-based, boolean/time-based behavior.
- [ ] DBMS fingerprint and injection stability.

### Condition → attack
- **Unsafely concatenated input reaches SQL query** → SQL injection.
- **SQLi with DB privileges / vulnerable configuration** → database read/write and possibly OS-level escalation.

### Tools / outcomes
- `Burp Suite` → manual confirmation/replay.
- `sqlmap` → automated detection/enumeration/exploitation.
- `searchsploit` → product/version-specific DB/app exploit lookup when relevant.

---

# 5. XSS branch

### Check
- [ ] Reflected inputs.
- [ ] Stored fields/comments/profiles.
- [ ] DOM sinks and JavaScript-controlled parameters.
- [ ] Output encoding/context.

### Condition → attack
- **User-controlled data returned in an executable browser context** → reflected/stored/DOM XSS.
- **Stored XSS reaches privileged user/admin** → higher-impact session/action abuse depending on application behavior.

### Tool
- `XSStrike` + `Burp Suite`.

---

# 6. Command injection branch

### Check
- [ ] Parameters controlling ping/dns/file conversion/diagnostic/system functions.
- [ ] Observe output, timing and error differences.
- [ ] Test safe command-separator variants appropriate to the application/OS.

### Condition → attack
- **Server-side input reaches OS command without safe separation** → command injection → OS command execution.
- **OS command execution + outbound connectivity** → reverse shell / pivot.

### Payload tools/artifacts
- `Burp Suite`
- `curl`
- `msfvenom` payloads when appropriate
- Bash/PowerShell/PHP reverse-shell patterns from `CommonPayload/`

---

# 7. File upload branch

### Check
- [ ] Upload extensions, MIME checks, content inspection, filename handling.
- [ ] Determine upload directory and whether uploaded files become executable.
- [ ] Test double extensions, alternate content types and server-side parser behavior where represented in the notes.

### Condition → attack
- **Uploaded server-side executable interpreted by web server** → web shell / code execution.
- **Uploaded file stored in privileged/unsafe path** → file overwrite or secondary impact.

### Outcome
- Web-command execution → shell upgrade → host LPE.

### Tools/artifacts
- `Burp Suite`
- `ffuf`
- PHP webshell pattern from the notes
- Python PTY upgrade after shell access

---

# 8. LFI branch

### Check
- [ ] Parameters such as `file=`, `page=`, template/include paths.
- [ ] Path traversal behavior and normalization.
- [ ] Whether source files, logs, configs or keys can be included/read.

### Condition → attack
- **User input controls local file path** → LFI/path traversal.
- **LFI can read source/config/credential material** → credential disclosure.
- **LFI reaches a controllable log or script path where the note's technique applies** → possible code execution path.

### Tools
- `Burp Suite`
- `ffuf`
- `SecLists` LFI wordlists

---

# 9. XXE branch

### Check
- [ ] XML endpoints, SOAP, upload/import features.
- [ ] Error differences with external entities / DTD processing.
- [ ] Whether the parser allows local file reads or network callbacks.

### Condition → attack
- **XML parser resolves attacker-controlled external entities** → XXE.
- **XXE + local file access** → sensitive file disclosure.
- **XXE + network access** → SSRF-style impact where the parser behavior permits it.

### Tool
- `XXEinjector`
- `Burp Suite`

---

# 10. HTTP Verb Tampering

### Check
- [ ] Compare response/authorization behavior for alternate methods.
- [ ] Test protected endpoints with the application's accepted verbs.

### Condition → attack
- **Authorization enforced for one method but not another** → verb-tampering access-control bypass.

### Tools
- `Burp Suite`
- `curl`

---

# 11. IIS tilde short-name enumeration

### Check
- [ ] Target is IIS and behaves like the note's vulnerable/observable configuration.
- [ ] Tilde/8.3 short-name behavior is detectable.

### Condition → attack
- **Short-name disclosure behavior present** → enumerate truncated file/folder names → discover hidden application resources.

### Tool
- `IIS-ShortName-Scanner`

---

# 12. LDAP Injection

### Check
- [ ] Web application performs LDAP search/authentication using user-controlled input.
- [ ] Input can affect LDAP filter/query structure.
- [ ] Error or auth behavior changes under LDAP metacharacters.

### Condition → attack
- **Unsafely concatenated LDAP input** → LDAP injection → authentication bypass, directory enumeration or data access depending on query logic.

### Tools
- `Burp Suite`
- `curl`
- LDAP tooling for follow-up enumeration (`ldapsearch` where applicable).

---

# 13. CMS / application-specific checklist

## WordPress

- [ ] Detect WordPress.
- [ ] Enumerate version/plugins/themes/users with `WPScan`.
- [ ] Identify outdated/vulnerable components.
- [ ] Check admin/authentication surface and exposed XML-RPC/admin functionality as represented in the notes.
- [ ] Match version/component findings to known exploit paths; use `searchsploit` / Metasploit only when the version/condition matches.

### Condition → attack
- **Vulnerable plugin/theme/component** → component-specific exploit.
- **Valid admin credential** → authenticated WordPress attack paths.
- **Admin-level code execution/upload capability** → shell → host LPE.

### Tools / outcome
- `WPScan` → component/version enumeration.
- `Burp Suite`, `curl` → request testing.
- `Metasploit` → module execution where the note's module/condition matches.
- Outcome: authenticated access, code execution, or shell.

---

## Drupal

- [ ] Fingerprint Drupal from page strings/changelog.
- [ ] Run `droopescan`.
- [ ] Identify Drupal version and enabled functionality.
- [ ] Check the historical admin/PHP-filter condition represented in the notes.

### Condition → attack
- **Pre-Drupal-8 admin + PHP Filter module enabled/available as described in notes** → PHP code evaluation → web/app code execution.
- **Known vulnerable version/module** → version-matched exploit path.

### Tools / outcome
- `droopescan`
- `Burp Suite`
- `searchsploit` when matching version/component.
- Outcome: web code execution → shell.

---

## Joomla

- [ ] Fingerprint Joomla and version.
- [ ] Enumerate components/extensions exposed in the installation.
- [ ] Match the exact version/component to the attack notes or a version-specific exploit record.

### Condition → attack
- **Known vulnerable Joomla component/version** → component-specific exploit.
- **Valid administrative access** → authenticated application attack paths.

### Tools
- `droopescan`
- `Burp Suite`
- `searchsploit`
- `Metasploit` where a matching module exists in the notes.

---

## GitLab

- [ ] Identify GitLab version.
- [ ] Compare version to the note's explicitly referenced **13.10.2-era RCE exploit**.
- [ ] Validate authentication/precondition requirements before running version-specific exploit code.

### Condition → attack
- **Matching GitLab version/conditions from the notes** → `gitlab_13_10_2_rce.py` / ExploitDB 49951 path → RCE.
- **RCE achieved** → shell/payload delivery → local privilege escalation or credential extraction.

### Tool
- `gitlab_13_10_2_rce.py`
- `searchsploit`
- `Burp Suite`

---

## PRTG Network Monitor

- [ ] Fingerprint/version the exposed PRTG instance.
- [ ] Check the note's credentials/version/configuration attack conditions.
- [ ] Search version-specific exploit information only after matching the target version.

### Condition → attack
- **Vulnerable PRTG version/configuration** → product-specific exploit path → application or OS-level access.

### Tools
- `nmap`
- `Burp Suite`
- `searchsploit`
- `Metasploit` when the note's matching module/path applies.

---

## Splunk

- [ ] Identify Splunk web/API and version.
- [ ] Check exposed authentication, application functionality and configuration locations represented in the notes.
- [ ] Investigate accessible scripts/configs for credential/code-execution opportunities.

### Condition → attack
- **Weak/exposed Splunk function or vulnerable version** → version/function-specific attack → code execution or credential disclosure.

### Tools
- `nmap`
- `Burp Suite`
- `curl`
- `searchsploit`

---

## osTicket

- [ ] Identify osTicket version and application endpoints.
- [ ] Enumerate public/admin areas and exposed functionality.
- [ ] Check exact version/component conditions against the note's attack path.

### Condition → attack
- **Known vulnerable component/version** → web exploit path → application access or code execution depending on the vulnerability.

### Tools
- `nmap`
- `Burp Suite`
- `searchsploit`

---

## ColdFusion

- [ ] Fingerprint ColdFusion/version.
- [ ] Enumerate standard administration/application endpoints.
- [ ] Match version to known exploit references before attacking.

### Condition → attack
- **Known vulnerable ColdFusion version/configuration** → version-specific exploit → server-side access.

### Tools
- `nmap`
- `searchsploit`
- `Metasploit`

---

## Apache Tomcat / Tomcat Manager

- [ ] Fingerprint Tomcat version.
- [ ] Check `/manager`-style application exposure.
- [ ] Test credentials where authorized.
- [ ] Determine whether manager functionality allows deployment.

### Condition → attack
- **Tomcat Manager exposed + valid manager credentials** → application/WAR deployment path → server-side code execution.
- **Vulnerable Tomcat/CGI configuration** → CGI-specific code-execution path.

### Tools
- `nmap`
- `curl`
- `Burp Suite`
- `Metasploit` (`tomcat_mgr_login` / matching module path represented in the notes).

---

## Jenkins

- [ ] Fingerprint Jenkins version.
- [ ] Check unauthenticated/weakly protected endpoints.
- [ ] Check whether script console or equivalent execution capability is exposed.

### Condition → attack
- **Accessible script console with sufficient privilege** → Groovy/script execution → OS command execution.
- **Vulnerable Jenkins version/plugin** → matching exploit → RCE.

### Tools
- `Burp Suite`
- `curl`
- `Metasploit` (`jenkins_script_console` / matching module path represented in the notes).

---

# Web SOP — Low access → host compromise

## Phase 1 — Recon

1. [ ] `nmap` ports/services.
2. [ ] Fingerprint with `Wappalyzer`, `curl`, `wafw00f`.
3. [ ] Screenshot/triage with `EyeWitness` when many targets exist.
4. [ ] Content discovery with `ffuf` / `gobuster`.
5. [ ] Enumerate subdomains/vhosts as relevant.

## Phase 2 — Attack surface mapping

1. [ ] Authentication/roles.
2. [ ] Parameters/IDs.
3. [ ] File uploads.
4. [ ] API/XML endpoints.
5. [ ] Technology-specific admin panels and management interfaces.

## Phase 3 — Highest-signal vulnerability checks

1. [ ] IDOR/access control.
2. [ ] SQLi.
3. [ ] Command injection.
4. [ ] File upload.
5. [ ] LFI.
6. [ ] XSS.
7. [ ] XXE.
8. [ ] HTTP Verb Tampering.
9. [ ] Product/version-specific exploits.

## Phase 4 — Application-specific branch

1. [ ] WordPress → `WPScan`.
2. [ ] Drupal/Joomla → `droopescan` + version/component checks.
3. [ ] GitLab → exact-version check; note's 13.10.2 exploit path.
4. [ ] PRTG/Splunk/osTicket/ColdFusion → version/configuration match.
5. [ ] Tomcat → Manager/CGI.
6. [ ] Jenkins → script-console/version/plugin path.

## Phase 5 — Convert web access to host access

1. [ ] Web code execution → choose shell/payload artifact from `CommonPayload/`.
2. [ ] Transfer files only when necessary using a note-supported transfer mechanism.
3. [ ] Upgrade weak shell (e.g. Python PTY where applicable).
4. [ ] Immediately switch to Windows/Linux privilege-escalation checklist.
5. [ ] Use recovered credentials to attack other services/hosts.

---

# Burnout / panic decision tree

```text
START
  |
  v
What is the technology?
  |-- UNKNOWN --> nmap + curl + Wappalyzer + EyeWitness
  |
  v
Find hidden content/parameters
  |-- ffuf / gobuster / SecLists / Burp
  |
  v
Is there auth/access control?
  |-- YES --> IDOR / verb tampering / role checks
  |
  v
Is input reaching a backend parser/interpreter?
  |-- SQL --> SQLi / sqlmap
  |-- XML --> XXE / XXEinjector
  |-- OS --> command injection
  |-- FILE --> LFI / upload
  |-- HTML/JS --> XSS / XSStrike
  |
  v
Is it a known application?
  |-- WORDPRESS --> WPScan
  |-- DRUPAL/JOOMLA --> droopescan + version/component checks
  |-- GITLAB --> version check / note's 13.10.2 path
  |-- TOMCAT --> manager / CGI
  |-- JENKINS --> script console / version
  |-- OTHER --> version + searchsploit + note-specific path
  |
  v
Do I have RCE?
  |-- YES --> shell -> transfer/upgrade -> Windows/Linux LPE
  |
  v
No RCE? Preserve access, enumerate deeper, test credentials and pivot.
```

## “Completely stuck” fallback order

1. [ ] `nmap`
2. [ ] `Wappalyzer` / `curl`
3. [ ] `ffuf` / `gobuster`
4. [ ] Burp request/response comparison
5. [ ] IDOR / verb tampering
6. [ ] SQLi / `sqlmap`
7. [ ] LFI / upload / command injection / XXE
8. [ ] Identify application and run its dedicated branch
9. [ ] Version-specific `searchsploit` / Metasploit check

## Source

[TamHoTsun-Alvin/CPTS_Notes](https://github.com/TamHoTsun-Alvin/CPTS_Notes) — especially `WebAttacks/`, `CommonPayload/`, and supporting `Generic/` and `Service Enumeration and Attack/` notes.
