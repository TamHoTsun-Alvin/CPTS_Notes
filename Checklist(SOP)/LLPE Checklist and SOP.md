### Linux Privilege Escalation Checklist

- Low-priv shell → id, sudo -l, uname -a, cat /etc/os-release, find SUID/SGID, capabilities (getcap), cron jobs, writable /etc/passwd or shadow, NFS mounts, environment variables.
- Sudo rights (NOPASSWD or specific binaries) → GTFOBins abuse → root.
- SUID/SGID binaries → GTFOBins or custom exploit.
- Capabilities (cap_setuid, cap_dac_override, etc.) → abuse.
- Writable cron jobs / scripts run as root → inject commands.
- LD_PRELOAD / shared-object hijacking / Python library injection if path controllable.
- Kernel exploits (uname + public CVEs).
- Weak NFS (no_root_squash) → plant SUID binary.
- Tmux / screen session hijacking if other users have sessions.
- LinPEAS for automated coverage.
- Restricted shell escape techniques.

**Linux Privesc SOP**

1. Basic enum (id, sudo -l, SUID, capabilities, cron, mounts, env).
2. Transfer & run LinPEAS → focus on red/yellow.
3. Abuse sudo / SUID / capabilities / writable cron first.
4. Credential hunting in files / history / configs.
5. Shared-object / LD_PRELOAD / Python injection if applicable.
6. Kernel exploit only if confirmed vulnerable and safer paths exhausted.
7. Root → dump /etc/shadow, SSH keys, further pivot.