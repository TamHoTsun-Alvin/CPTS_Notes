When we first enter the same network range, we could first try to perform some Enumeration and see what exist inside the domain, we can use different tools to archive such purpose:

-Using wireshark
```
sudo -E wireshark
```

-Using tcpdump
```
sudo tcpdump -i <interface_that_connects_to_victim>
```

-Using Responder:
```
sudo responder -I <interface_that_connects_to_victim> -A 
```

We can also first enumerate list of possible username before actually mingling with the environment by using kerbrute with precompiled username list like jsmith / jsmith2, note that we need to first find out where DC is before we can do this

-Using kerbrute:
```
kerbrute userenum -d <domain> --dc <dcip> <usernamelist> -o valid_ad_users
```