**General**

- Directory / page / parameter / vhost fuzzing (ffuf) → hidden content.
- SQL Injection (manual ' / OR / UNION / comments + sqlmap) → data dump / RCE / auth bypass.
- Command Injection → RCE.
- File Upload (extension / content-type / path traversal / double extension) → web shell.
- LFI / path traversal → source code / credential files / RCE via log poisoning or PHP wrappers.
- XSS (reflected / stored) → session hijack / further attacks.
- XXE → file read / SSRF / RCE (in some parsers).
- IDOR → access other users’ data.
- HTTP Verb Tampering → bypass restrictions.
- Auth bypass / default credentials / weak password forms.

**Specific applications (from notes)**

- Apache Tomcat / CGI → manager default creds, WAR upload, CGI exploits.
- Jenkins → script console / default creds → RCE.
- Splunk → known vulns / default creds.
- osTicket → known vulns / file upload / SQLi.
- ColdFusion → known CVEs / admin interfaces.
- IIS → Tilde enumeration, short-name disclosure.
- LDAP Injection on web-facing LDAP forms.
- Generic servlet containers / management interfaces.

**Web Attack SOP**

1. Recon (ffuf dirs/pages/vhosts, technology fingerprint, default pages).
2. Test common injection points (SQLi, command, LFI, XXE, IDOR).
3. File upload / auth forms / admin interfaces.
4. Specific app checks (Tomcat manager, Jenkins, etc.).
5. Gain web shell / RCE → local privesc → pivot into internal network / AD if applicable.