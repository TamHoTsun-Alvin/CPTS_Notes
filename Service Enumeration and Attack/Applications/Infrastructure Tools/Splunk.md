Splunk is often seen on internal networks and uncommon to have one facing externally, it does often runs as root or SYSTEM, but there is not much exploits that would be useful to us

Enumeration:

Weak / Non-existent Credential:

On older version of splunk, it contains a default password that's displayed on the login panel `admin:changeme`, newer version have the password set on setup, however it is still very worth it to try weak password

It is also important to notice that, after 60days, if a valid license other then the trial license, it automatically switch to Free License and this License stripped away the ability for authentication and role management, meaning no authentication needed.

Attack:

Aside from browsing data, we can abuse functionality after we gain access to splunk, the repo https://github.com/0xjpuff/reverse_shell_splunk contains a simple reverse shell we can deploy to victim, for using this, refer to https://academy.hackthebox.com/app/module/113/section/1213