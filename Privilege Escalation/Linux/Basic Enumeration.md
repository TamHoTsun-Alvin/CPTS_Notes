The first step towards privilege escalation in Linux is to first enumerate our environment, automated tools and scripts will be moved towards a separate session, this session will focus on manual enumeration techniques.

The first thing when we land onto a host is to first know what we are dealing with, we can run the following commands to get some basic information:

- `whoami` - what user are we running as
- `id` - what groups does our user belong to?
- `hostname` - what is the server named, can we gather anything from the naming convention?
- `ifconfig` or `ip a` - what subnet did we land in, does the host have additional NICs in other subnets? (See if it contains other NIC to visit other networks)
- `route` or `netstat -rn` - what route does the server have to other networks?
- `sudo -l` - can our user run anything with sudo (as another user as root) without needing a password? This can sometimes be the easiest win and we can do something like `sudo su` and drop right into a root shell.

Aside from the above commands, we also can check what OS we are dealing with by looking at the /etc/os-release file:
```
cat /etc/os-release
```

We should also enumerate for the user's PATH and all environment variable:
```
echo $PATH
env
```

We should also enumerate kernel version, shells and cpu type to see if their is any kernel / other exploits we can use for easy win:
```
uname -a
lscpu
cat /etc/shells
```

We should look out for the following services, if they exist in the system, it means this system is hardened and we should be more careful in our attempt to enumeration:
- [Exec Shield](https://en.wikipedia.org/wiki/Exec_Shield)
- [iptables](https://linux.die.net/man/8/iptables)
- [AppArmor](https://apparmor.net/)
- [SELinux](https://www.redhat.com/en/topics/linux/what-is-selinux)
- [Fail2ban](https://github.com/fail2ban/fail2ban)
- [Snort](https://www.snort.org/faq/what-is-snort)
- [Uncomplicated Firewall (ufw)](https://wiki.ubuntu.com/UncomplicatedFirewall)

We can enumerate drives and shares on the system, maybe we can mount additional drives or share to expose sensitive files:
```
lsblk
```

We can enumerate `lpstat` to find printer information, we maybe could find sensitive data in /etc/fstab by performing a simple word search for password, username etc.

If the machine is domain joined, it would be nice to check `/etc/resolv.conf` for internal DNS record to gain a starting point to query AD (Knowing which ip the DC sits on etc.)

We can check for /etc/passwd file to enumerate the following information:
```
Avalon112@htb[/htb]$ cat /etc/passwd

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
<snip>
```

If we somehow gained access to /etc/shadow, it is wise for us to copy it locally to perform a offline password attack, the beginning of hash (`$?$...`) hint's what type of hash it is

We can enumerate existing groups to see 