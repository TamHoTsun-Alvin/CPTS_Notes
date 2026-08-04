After we prepared a list of possible usernames and password list, we can use different tools to validate users or attempt to bruteforce into account, Refer to [[Generating Possible Usernames]] 

-Using Kerbrute to Enumerate valid usernames

```
./kerbrute_linux_amd64 userenum --dc <dcip> --domain <domain> <username_candidate>
```

-