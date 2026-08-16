If we encounter programs that is custom written or seemingly under development, we can always check for the shared object required by it and attempt to hijack it, below we use payroll as program example, which is owned by root, even if it's not owned by root, we can also use it to gain access to another account:

Checking .so requirement:

```
ldd payroll

linux-vdso.so.1 =>  (0x00007ffcb3133000)
libshared.so => /development/libshared.so (0x00007f0c13112000)
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f7f62876000)
/lib64/ld-linux-x86-64.so.2 (0x00007f7f62c40000)
```

We noticed a libshred.so, which is listed as dependency, we can inspect where the RUNPATH is, RUNPATH is given preference over other folder when it comes to loading .so
```
readelf -d payroll  | grep PATH

 0x000000000000001d (RUNPATH)            Library runpath: [/development]
```

Next, we confirm if the path /development is writable by us, if the directory is set with lax permission, then we can use it to perform privilege escalation:

We first find another library to replace this /development/libshared.so, this preferably should be another library.
```
cp /lib/x86_64-linux-gnu/libc.so.6 /development/libshared.so 
```

Next, we run payroll, we should be bumped by a symbol lookup error ()
