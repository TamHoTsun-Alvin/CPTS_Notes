Splunk is often seen on internal networks and uncommon to have one facing externally, it does often runs as root or SYSTEM, but there is not much exploits that would be useful to us

Enumeration:

Weak / Non-existent Credential:

On older version of splunk, it contains a default password that's displayed on the login panel `admin:changeme`, newer version have the password set on setup, however it is still very worth it to try weak password

It is also important to notice that, after 60days, if a valid 