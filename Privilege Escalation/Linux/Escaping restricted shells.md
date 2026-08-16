Sometimes, we might land ourselves on restricted shells, we can attempt to escape the shell by several means:

Command Injection:

Sometimes we can try inject commands into options of commands allowed to run like the following example:
```
ls -l `pwd` 
```

Command Substituition:

Sometimes, we are able to use shell command substitution techniques 