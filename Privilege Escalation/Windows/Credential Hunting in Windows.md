During testing, whenever we have access to a system, we maybe able to get ourselves a easy win when we can find credentials for other users in the system, even though the credentials we found is not useful now it is still worth it, as maybe we can use it to access other systems.

Searching credentials in file:

Often we can find passwords or credentials stored in cleartext config files or note file, sometimes this might be the doing of an installed application or a user that afraid he/she forgets the password, we can use findstr to search for such sensitive information:
```
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml
```

We can replace password with other keyword as well, like pass, admin or other keyword we wish to find