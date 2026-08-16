We should actively look for configuration files, scripts, bash history, backup files or files that's stored in webroot:

```
grep 'DB_USER\|DB_PASSWORD' wp-config.php
find / ! -path "*/proc/*" -iname "*config*" -type f 2>/dev/null
```

We also can check for ~/.ssh for saved ssh keys and known host file to enumerate our next target:
```
ls ~/.ssh

id_rsa  id_rsa.pub  known_hosts
```