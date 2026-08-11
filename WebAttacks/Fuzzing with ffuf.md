Using ffuf, we can fuzz a lot of things, below is some examples on how to perform fuzzing on different items

Directory Fuzzing:

We can use ffuf to fuzz for directory by utilizing ffuf like the following Example:
```
ffuf -w <pathtowordlist> -u http://SERVER_IP:PORT/FUZZ
```

Page Fuzzing:

Aside from directory, we can also fuzz for pages that we can visit, before fuzzing for other page we need to determine what kind of extension the pages are, therefore we fuzz for index page extension first, we can do so by using the web-extensions.txt wordlist:

```
ffuf -w <pathtowordlist> -u http://SERVER_IP:PORT/blog/indexFUZZ
```

After confirming, we can then fuzz for actual pages, we can use the directory-list-2.3 wordlist
```
ffuf -w <pathtowordlist> -u http://SERVER_IP:PORT/blog/FUZZ.php
```


Recursive Fuzzing:

Enabling recursive fuzzing allows ffuf to automatically look for files and directory when a new directory is discovered, it can be utilized in the following way:
```
ffuf -w <pathtowordlist> -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth <depth> -e <extentions> -v
```


Sub-Domain Fuzzing:

We can fuzz for subdomains as well, subdomains refer to a domain under a specific domain that looks like this:

https://chickenstocktasty.net --> https://admin.chickenstocktasty.net

We can use the subdomains-top1million wordlist to do it:
```
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.inlanefreight.com/
```

