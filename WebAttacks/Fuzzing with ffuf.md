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
ffuf -w <pathtowordlist> -u http://SERVER:PORT/FUZZ -recursion -recursion-depth <depth> -e <extentions> -v
```


Sub-Domain Fuzzing:

We can fuzz for subdomains as well, subdomains refer to a domain under a specific domain that looks like this:

https://chickenstocktasty.net --> https://admin.chickenstocktasty.net

We can use the subdomains-top1million wordlist to do it:
```
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.inlanefreight.com/
```

Vhost fuzzing:

If we suspect that different pages are served with vhost instead of subdomain, then we need to perform Vhost Fuzzing instead, without actually adding the wordlist to our host, we can fuzz the Host header instead in the following way, we can do this with the subdomain-top1million wordlist to perform this task:

```
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://SERVER:PORT/ -H 'Host: FUZZ.<server>'
#Example:
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb'
```

Response filtering:

We can filter our response if we know what is actually useful to us, below is ways to filter:

Filtering with response size:

Let's say we know that the response size when we request a non-existent page, assuming it to be 1043, then we can filter out all response with this size by specifying the option -fs 1043

Filtering with status code:

Let's say we are know that we are looking for a specific code, 200, any other code is garbage, then we can specify such using the option -mc 200, if we are looking for more then 1 code, then comma separate it like the following:

-mc 200, 301, 401

If instead, we are uncertain what code is gold but know what code is garbage, we can have them filtered out by specifying the option -fc instead, like -mc, we can use comma separate for filtering multiple code:

-fc 403, 404

Parameter Fuzzing:

Sometimes, it would be essential for us to fuzz the parameter before we can continue, we can utilize the burp-parameter-names.txt wordlist to archive such

```
ffuf -w <pathtowordlist> -u <urltofuss> -fs xxx
#Example:
ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key -fs xxx
```

We can also fuzz it with POST as the method like the following example:
```
ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx
```

Value Fuzzing:

Finally, for post value fuzzing, we can refer to this example:

```
ffuf -w ids.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx
```
#Note: for parameter / value fuzzing, it would be more convenient if we are doing it in burp, therefore even though CE Version of burp suite only support 1req/s, it is worth considering

LFI Fuzzing:

We can fuzz for payloads that works against a certain website that can archive LFI instead of crafting manually, to do so, we can use the LFI-Jhaddix.txt wordlist https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt and perform it in a manner similar to our example:
```
ffuf -w <pathtowordlist> -u 'http://<SERVER_IP>:<PORT>/index.php?language=FUZZ' 
```

Webroot Fuzzing:

We can fuzz for webroots in case we dont know where the file we uploaded are placed or we simply wish to know where the webroot is located, we can use https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt for linux and https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt for windows, we can perform the fuzz in the following manner and remember to target index page, which is almost present on all website:
