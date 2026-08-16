Sometimes, we might land ourselves on restricted shells, we can attempt to escape the shell by several means:

Command Injection:

Sometimes we can try inject commands into options of commands allowed to run like the following example:
```
ls -l `pwd` 
```

Command Substitution:

Sometimes, we are able to use shell command substitution techniques. For example, if we are not allowed to directly read flag.txt, we can instead echo it out by telling the system to substitute our input for echo by the content of flag.txt
```
echo $(<flag.txt)
```

Command Chaining: 

Sometimes, we can execute command not allowed by Chaining it behind an allowed command:
```
echo hi | pwd
```

Environment Variables:

If we can modify or create environment variables, sometimes we can escape the shell by modifying some environment variable in specifying another directory

Shell Functions:

If we can define shell functions, we might be able to execute command that is restricted by shell by calling the shell function we defined

