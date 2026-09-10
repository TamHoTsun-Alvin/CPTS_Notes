# CPTS — Windows Privilege Escalation Checklist & SOP

> Objective: determine what **local conditions** exist, map each condition to the shortest privilege-escalation path, then stop once the required privilege is reached. Scope follows the main notes; `Extras/` is excluded.

---

## 0. Initial state

- [ ] Record hostname, username, architecture, OS/version, and current shell type.
- [ ] `whoami /all`-style information: current user, groups, privileges.
- [ ] Determine whether the account is local/domain and whether it has useful built-in group membership.
- [ ] Check whether the host is domain joined.
- [ ] Run **WinPEAS** and/or **SharpUp** early for breadth.
- [ ] Use manual checks to validate the highest-value findings.

### Tools
- `WinPEAS.exe`
- `SharpUp.exe`
- `accesschk.exe`
- `PsService.exe`
- `pipelist.exe`
- `procdump.exe`
- `LaZagne.exe`
- `psgetsystem`
- `EnableAllTokenPrivs`

---

## 1. Privilege / token checks

### Condition → attack

- **SeImpersonatePrivilege / suitable impersonation privileges** → try the note-supported Potato/PrintSpoofer family → **SYSTEM**.
- **SeDebugPrivilege** → `psgetsystem` / token-manipulation path → **SYSTEM** when applicable.
- **SeBackupPrivilege** → protected file/registry/NTDS read workflow → credential material → domain/local escalation.
- **Assigned but disabled privileges** → `EnableAllTokenPrivs` → expose usable privilege escalation paths.
- **Built-in privileged group membership** → inspect group-specific rights before trying generic exploits.

### Tools / outcome
- `JuicyPotato` / `PrintSpoofer` / `RoguePotato` → SYSTEM on suitable versions/conditions.
- `psgetsystem` → SYSTEM.
- `EnableAllTokenPrivs` → usable token privileges.

---

## 2. Service misconfiguration

### Check
- [ ] Enumerate services, service binary paths, start accounts, permissions.
- [ ] Identify services where the current account can write the binary, directory, or service configuration.
- [ ] Check for quoting/path ambiguity and weak service-folder permissions when the notes flag them.

### Condition → attack
- **Current user can modify service binary or service path** → service hijack/replacement → service starts as high-privileged account → usually SYSTEM.
- **Writable service directory** → replace/plant executable or DLL when execution conditions fit.
- **Weak service configuration permissions** → modify service settings → restart as privileged identity.

### Tools / outcomes
- `SharpUp` → service weakness candidates.
- `accesschk` → confirm exact write/control permissions.
- `PsService` → enumerate/control service state.

---

## 3. AlwaysInstallElevated

### Check
- [ ] Verify the Windows Installer policy conditions required by the notes are present for both policy scopes.

### Condition → attack
- **AlwaysInstallElevated correctly enabled** → craft MSI payload with `msfvenom` → install through elevated Windows Installer context → **SYSTEM**.

### Outcome
- Direct SYSTEM command/shell when the policy is genuinely misconfigured.

---

## 4. DLL injection / DLL search-order hijacking

### Check
- [ ] Find a privileged process/service loading a missing or writable DLL.
- [ ] Identify a search path where the low-privilege user can write.
- [ ] Confirm the target process actually runs as a higher-privileged identity.

### Condition → attack
- **Privileged binary + writable DLL load location** → DLL hijack/injection → code execution as the process account.
- **Writable service/component directory** → place replacement library → trigger privileged execution.

### Outcome
- SYSTEM/admin execution depending on the target process.

---

## 5. Named pipes

### Check
- [ ] Enumerate interesting named pipes with `pipelist`.
- [ ] Identify weak permissions / impersonation opportunities.
- [ ] Determine which privileged process owns the pipe.

### Condition → attack
- **Privileged named pipe + exploitable permissions/impersonation condition** → named-pipe abuse → elevated token/SYSTEM path.

### Tool / outcome
- `pipelist.exe` → candidate pipe discovery.

---

## 6. Kernel / EOL OS branch

### Check
- [ ] OS version/build and patch level.
- [ ] Whether the system is end-of-life or obviously missing relevant security fixes.
- [ ] Whether the notes' kernel-exploit category matches the host.

### Condition → attack
- **Known vulnerable/EOL kernel condition** → kernel exploit → SYSTEM.

### Rule
- Treat kernel exploitation as a **later branch**, after faster configuration/credential paths are exhausted.

---

## 7. Credential hunting / pillaging

### Check
- [ ] Search files, scripts, configs and application stores.
- [ ] Inspect saved credentials/credential stores referenced by the notes.
- [ ] Look for domain/admin passwords, API secrets, connection strings, private keys.
- [ ] Check whether recovered credentials work on other hosts or for domain accounts.

### Condition → attack
- **Plaintext password / reusable secret** → authenticate as stronger account.
- **Credential for a privileged service** → service/admin access.
- **Domain password discovered locally** → AD lateral movement and possibly domain privilege.

### Tools / outcome
- `LaZagne.exe` → saved application credentials.
- `Snaffler.exe` / `PowerHuntShares` → share/file secrets.
- `dnSpy` → secrets in .NET binaries/configuration.
- `procdump.exe` → dumpable process data where permitted.

---

## 8. Default logon / desktop breakout / misc paths

- [ ] Check for default or stored logon username information.
- [ ] Assess whether the desktop/session is intentionally locked down and whether the notes' breakout techniques apply.
- [ ] Re-check local group membership and policy after obtaining a new account.
- [ ] Do not burn time on a generic “misc” branch if a direct service/token/credential path exists.

---

# Windows LPE SOP — Low access → SYSTEM

## Phase 1 — First foothold

1. [ ] Identify user/host/OS/architecture.
2. [ ] Run `WinPEAS` or `SharpUp`.
3. [ ] Check token privileges and groups manually.
4. [ ] Search for credentials and sensitive files.

## Phase 2 — Prioritize fast wins

Use this order unless enumeration clearly changes it:

1. [ ] **SeImpersonate / Potato / PrintSpoofer branch**.
2. [ ] **SeDebug / psgetsystem**.
3. [ ] **AlwaysInstallElevated**.
4. [ ] **Weak service / service binary / service path permissions**.
5. [ ] **DLL hijack/injection**.
6. [ ] **Named pipe**.
7. [ ] **Credential reuse**.
8. [ ] **Kernel exploit**.

## Phase 3 — Validate and capitalize

1. [ ] Confirm elevated identity.
2. [ ] Pillage credentials/files with the stronger privilege.
3. [ ] If domain joined, extract/reuse relevant credentials and return to the AD attack tree.
4. [ ] Record the cleanest proof of SYSTEM/admin access for the exam.

---

# Burnout / panic decision tree

```text
START
  |
  v
Who am I? Which OS? Which groups/privileges?
  |
  v
Run WinPEAS / SharpUp
  |
  v
Did I get a token-privilege finding?
  |-- YES --> SeImpersonate? -> Potato/PrintSpoofer/RoguePotato
  |             SeDebug?       -> psgetsystem
  |             SeBackup?      -> protected-file/credential path
  |
  v
AlwaysInstallElevated?
  |-- YES --> MSI payload -> SYSTEM
  |
  v
Weak service permissions?
  |-- YES --> service abuse -> SYSTEM
  |
  v
Writable DLL path / hijack?
  |-- YES --> DLL abuse -> privileged execution
  |
  v
Interesting named pipe?
  |-- YES --> inspect permissions / impersonation path
  |
  v
Credentials found?
  |-- YES --> reuse against local/domain/remote services
  |
  v
EOL/vulnerable kernel?
  |-- YES --> kernel path
  |
  v
Re-enumerate. Do not repeat a failed branch without a new condition.
```

## “Completely stuck” fallback order

1. [ ] `WinPEAS`
2. [ ] `SharpUp`
3. [ ] `whoami /all` / groups / privileges
4. [ ] SeImpersonate / SeDebug / SeBackup
5. [ ] AlwaysInstallElevated
6. [ ] Service permissions with `accesschk`
7. [ ] Credential hunting with `LaZagne` / share tools
8. [ ] Named pipes
9. [ ] Kernel/EOL branch

## Source

[TamHoTsun-Alvin/CPTS_Notes](https://github.com/TamHoTsun-Alvin/CPTS_Notes) — especially `Privilege Escalation/Windows/`, with supporting payload and AD notes.
