Password Spraying is a technique that we use 1 password to authenticate for a list of valid account that we are know of, one can use a commonly used weak password as the last resort to gain valid credentials, their is also a common situation where one would own more then one account in the company (Example cjack, cjack_ixadm, cjack_dev etc.), if we gained the password for cjack, we could try to perform password spraying as it's highly likely that cjack_ixadm and cjack_dev use same password or just a variation of the password used for cjack.

If possible, we would want to obtain the password policy first to avoid account lockout

-Obtaining password policy with nxc (Default, with credential):
```
nxc smb 172.16.5.5 -u <username> -p <password> --pass-pol
```

-Obtaining password policy with enum4linux-ng (Default, without credential):
```
enum4linux-ng -P <dcip> -oA <outputfile_name>
```
