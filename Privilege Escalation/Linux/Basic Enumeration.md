The first step towards privilege escalation in Linux is to first enumerate our environment, automated tools and scripts will be moved towards a separate session, this session will focus on manual enumeration techniques.

The first thing when we land onto a host is to first know what we are dealing with, we can run the following commands to get some basic information:

- `whoami` - what user are we running as
- `id` - what groups does our user belong to?
- `hostname` - what is the server named, can we gather anything from the naming convention?
- `ifconfig` or `ip a` - what subnet did we land in, does the host have additional NICs in other subnets? (See if it contains other NIC to visit other networks)
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

We should also enumerate kernel version, shells and cpu type to see if their is any kernel / 