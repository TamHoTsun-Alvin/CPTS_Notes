# CPTS — Linux Privilege Escalation Checklist & SOP

> Objective: move from an unprivileged shell to root by matching **enumerated conditions** to the shortest note-supported escalation path. `Extras/` is excluded.

---

## 0. Initial shell triage

- [ ] Identify user and groups.
- [ ] Identify kernel/OS/architecture.
- [ ] Check current shell type and whether it is restricted.
- [ ] Check current working directory, home directories and obvious application roots.
- [ ] Run `linpeas.sh` early.
- [ ] Run `pspy` when scheduled/background activity may matter.

### Tools
- `linpeas.sh`
- `pspy`
- `GTFOBins` / `LOLBins` reference from the notes
- `hashcat` / `John` if hashes are recovered

---

## 1. Sudo branch

### Check
- [ ] Enumerate `sudo` privileges.
- [ ] Check whether a command can run as root without a password.
- [ ] Check for commands whose built-in functionality permits command/file escape.

### Condition → attack
- **`sudo` permission to run exploitable command as root** → sudo abuse → root.
- **Sudo rule permits command with user-controlled arguments/files/environment** → escape to root.

### Tool/reference
- `GTFOBins` as the reference catalog for note-supported binaries.

---

## 2. SUID / SGID branch

### Check
- [ ] Enumerate SUID/SGID executables.
- [ ] Compare interesting binaries against `GTFOBins`.
- [ ] Check whether binaries are custom, writable, old, or unusual.

### Condition → attack
- **SUID/SGID binary has a command/file escape** → execute as file owner, often root.
- **Custom privileged binary has unsafe behavior** → local code execution as privileged owner.

### Outcome
- Root shell or privileged file read/write.

---

## 3. Linux capabilities branch

### Check
- [ ] Enumerate file capabilities.
- [ ] Identify binaries with powerful capabilities assigned to non-root-executed files.
- [ ] Match the capability + binary to an escape technique from the notes/reference.

### Condition → attack
- **Powerful capability on attacker-invokable binary** → capability abuse → root-equivalent action or privileged file/process control.

---

## 4. Cron jobs / scheduled tasks

### Check
- [ ] Enumerate cron jobs.
- [ ] Check scripts called by root jobs.
- [ ] Check write permission on the script and parent directories.
- [ ] Use `pspy` to confirm execution timing/arguments when needed.

### Condition → attack
- **Root cron executes writable script/file** → modify script → wait for trigger → root.
- **Root cron executes binary from writable path** → replace/plant binary → root execution.

### Tools / outcome
- `linpeas` → cron findings.
- `pspy` → live confirmation of scheduled execution.
- Outcome: root command execution.

---

## 5. Logrotate branch

### Check
- [ ] Determine whether privileged log rotation is running.
- [ ] Check whether log files, configs or helper scripts are writable/controllable.
- [ ] Match the host to the note's logrotate attack conditions.

### Condition → attack
- **Writable logrotate-related component + privileged rotation** → logrotate abuse → root.

---

## 6. LD_PRELOAD branch

### Check
- [ ] Find a privileged execution path that honors `LD_PRELOAD` under the note's conditions.
- [ ] Confirm environment/library write conditions.

### Condition → attack
- **Privileged process + controllable `LD_PRELOAD` path** → malicious shared library loaded → root code execution.

---

## 7. Shared object hijacking

### Check
- [ ] Identify privileged applications loading shared libraries.
- [ ] Check library search path and write permissions.
- [ ] Confirm privileged trigger/execution frequency.

### Condition → attack
- **Privileged binary + writable library/search-path component** → shared-object hijack → root.

---

## 8. Python injection / interpreter abuse

### Check
- [ ] Find privileged Python execution referenced by the notes.
- [ ] Check for writable Python modules, scripts or import paths.

### Condition → attack
- **Root executes attacker-controlled Python module/code path** → Python injection → root.

---

## 9. tmux hijacking

### Check
- [ ] Identify tmux sessions owned by another/high-privilege user.
- [ ] Check session/socket permissions and whether current user can access the session.

### Condition → attack
- **Accessible privileged tmux session** → attach/hijack session → privileged command execution.

---

## 10. Weak NFS / exports

### Check
- [ ] Enumerate NFS exports.
- [ ] Check anonymous/root mapping and write permissions.
- [ ] Check whether an exported path is used by root or contains privileged startup/configuration material.

### Condition → attack
- **Writable export with unsafe root mapping** → modify privileged files / payload placement → root.

### Tools / outcome
- `showmount`
- `mount -t nfs`
- Outcome: direct file access or root path through weak export configuration.

---

## 11. Restricted shell escape

### Check
- [ ] Identify whether the shell restricts commands, paths or environment.
- [ ] Identify allowed binaries with shell escape capabilities.
- [ ] Use the note-supported built-in-functionality/GTFOBins route.

### Condition → attack
- **Restricted shell + permitted binary that can spawn a shell** → shell breakout → normal shell.

---

## 12. Privileged groups

### Check
- [ ] Enumerate group memberships.
- [ ] Compare membership to groups with access to devices, logs, containers, backups, disks, etc. as covered in the notes.

### Condition → attack
- **Privileged group membership** → direct group-specific privilege abuse → root or sensitive-data access.

---

## 13. Credential hunting

### Check
- [ ] Search home directories, configs, scripts, history and application files.
- [ ] Look for passwords, private keys, API tokens and service credentials.
- [ ] Check for Kerberos keytabs.

### Condition → attack
- **Plaintext credential** → authenticate as stronger user/service.
- **Private key** → SSH/remote access.
- **Keytab** → Kerberos authentication path / key extraction.
- **Recovered hash** → offline crack with `hashcat` / `John`.

### Tools / outcome
- `linpeas`
- `LaZagne.py`
- `KeyTabExtract`
- `hashcat` / `John`

---

## 14. Kernel exploit branch

### Check
- [ ] Kernel version and distribution.
- [ ] Patch level and EOL status.
- [ ] Confirm the host matches a note-supported vulnerable-kernel condition.

### Condition → attack
- **Vulnerable kernel + matching exploit conditions** → kernel exploit → root.

### Rule
- Use kernel exploitation late; it is noisier and usually less reliable than clear configuration/credential findings.

---

# Linux LPE SOP — Low access → root

## Phase 1 — Establish facts

1. [ ] `id`, groups, OS/kernel, architecture.
2. [ ] Run `linpeas.sh`.
3. [ ] Run `pspy` if cron/process timing matters.

## Phase 2 — Fastest branches

1. [ ] `sudo` permissions.
2. [ ] SUID/SGID + GTFOBins.
3. [ ] Capabilities.
4. [ ] Writable cron/logrotate.
5. [ ] LD_PRELOAD.
6. [ ] Shared object hijack.
7. [ ] tmux hijack.
8. [ ] NFS.
9. [ ] Privileged groups.
10. [ ] Credential hunting.
11. [ ] Kernel exploit.

## Phase 3 — Validate

1. [ ] Confirm `id`/effective UID is root.
2. [ ] Collect any credentials/configs needed for lateral movement.
3. [ ] If domain/enterprise infrastructure is involved, return to the AD/protocol checklist.

---

# Burnout / panic decision tree

```text
START
  |
  v
Who am I / what kernel?
  |
  v
Run linpeas
  |
  v
sudo rule?
  |-- YES --> GTFOBins / sudo abuse -> root
  |
  v
SUID/SGID?
  |-- YES --> GTFOBins / custom binary -> root
  |
  v
Capabilities?
  |-- YES --> capability abuse -> root
  |
  v
Writable cron/logrotate?
  |-- YES --> modify privileged task -> root
  |
  v
LD_PRELOAD / shared object path?
  |-- YES --> library hijack -> root
  |
  v
Interesting tmux / NFS?
  |-- YES --> session/export abuse -> root
  |
  v
Credentials/keytab?
  |-- YES --> reuse/extract/crack -> stronger user
  |
  v
Kernel vulnerable?
  |-- YES --> kernel path -> root
  |
  v
Re-run enumeration after every privilege change.
```

## “Completely stuck” fallback order

1. [ ] `linpeas.sh`
2. [ ] `sudo -l`-style check
3. [ ] SUID/SGID
4. [ ] capabilities
5. [ ] cron / `pspy`
6. [ ] credentials / keytabs
7. [ ] NFS
8. [ ] logrotate / LD_PRELOAD / shared-object paths
9. [ ] kernel

## Source

[TamHoTsun-Alvin/CPTS_Notes](https://github.com/TamHoTsun-Alvin/CPTS_Notes) — especially `Privilege Escalation/Linux/` and supporting `FileTransfer/` / `CommonPayload/` notes.
