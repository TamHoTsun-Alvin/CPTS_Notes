If we encounter programs that is custom written or seemingly under development, we can always check for the shared object required by it and attempt to hijack it, below we use payroll as program example:

Checking .so requirement:

```
ldd payroll

linux-vdso.so.1 =>  (0x00007ffcb3133000)
libshared.so => /development/libshared.so (0x00007f0c13112000)
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f7f62876000)
/lib64/ld-linux-x86-64.so.2 (0x00007f7f62c40000)
```

We noticed a libshred.so, which is listed as dependency.