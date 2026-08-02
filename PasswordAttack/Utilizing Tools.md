Hashcat:

-Identifying hash type:
```
hashid -m '<hash> or <path_to_hashfile>'
```
-Cracking a hash:
```
hashcat -a <0-dictionary/3-mask> -m <mode> <hash/hashfile> <path_to_wordlist / mask> -r <ruleset, optional>
```

JohnTheRipper:

-Identifying hash type:
```
hashid -j '<hash> or <path_to_hashfile>'
```
-