If we encounter logrotate on system and the following is met:
- we need `write` permissions on the log files
- logrotate must run as a privileged user or `root`
- vulnerable versions:
    - 3.8.6
    - 3.11.0
    - 3.15.0
    - 3.18.0

Then we can refer to https://academy.hackthebox.com/app/module/51/section/1589 for privesc