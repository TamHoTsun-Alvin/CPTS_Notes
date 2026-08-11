Using ffuf, we can fuzz a lot of things, below is some examples on how to perform fuzzing on different items

Directory Fuzzing:

We can use ffuf to fuzz for directory by utilizing ffuf like the following Example:
```
ffuf -w <pathtowordlist> -u http://SERVER_IP:PORT/FUZZ
```