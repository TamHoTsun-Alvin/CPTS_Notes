Sometimes, we might land ourselves on restricted shells, we can attempt to escape the shell by several means:

Command Injection:

Sometimes we can try inject commands into options of commands allowed to run like the following example:
```
ls -l `pwd` 
```

Command Substituition:

Sometimes, we are able to use shell command substitution techniques. For example, if we are not allowed to directly read flag.txt, we can instead echo it out by telling the system to substitute our input for echo by the content of flag.txt
```
echo $(<flag.txt)
```
