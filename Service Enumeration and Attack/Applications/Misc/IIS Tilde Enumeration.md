IIS Tilde Enumeration is a technique that can be used to reveal hidden files, directories under different version of IIS webserver, it originates from the 8.3 naming format:

For a path / filename, a short name is generated following the following convention:
-first 6 characters of the original filename
-a tilde (~)character
-a sequence number (combined with previous 2 is exactly 8 character)
-If it's filename, a dot (.) and file extension

Example: 

Directoryname: Documents -> Docume~
Filename: gugugaga.txt -> guguga~.txt

Due to this naming convention, we now no longer need to know the actual directory and file name, instead we simply enumerate possible combinations

To enumerate, we first send HTTP request to server with 1 character following a tilde:
```
http://example.com/~a
http://example.com/~b
http://example.com/~c
<snip>
```
Assuming a HTTP200 OK is returned to us at the character s, we continue this
```
http://example.com/~se
http://example.com/~sf
http://example.com/~sg
<snip>
```
until we revealed the first part of the shortname (assuming it's the word secret), then we simply stuff the number part
```
http://example.com/secret~1
http://example.com/secret~2
<snip>
```

We can then continue enumerating or access files in the following manner:
```
http://example.com/secret~1/applerecipe.txt
http://example.com/secret~1/appler~1.txt
<snip>
```

Enumeration:

First, we need to detect the presence of an IIS server, where we can confirm such when seeing the string `Microsoft IIS httpd` when enumerating with nmap

Automation:
We can use IIS-ShortName-Scanner at https://github.com/irsdl/IIS-ShortName-Scanner, note that using this tool requires java, after configuration we can use it like the following:
```
java -jar iis_shortname_scanner.jar 0 5 <url>
```

Sometimes, we can't access files directly using the 8.3 name, still we need to bruteforce the page with a customed wordlist, to do so we can follow https://academy.hackthebox.com/app/module/113/section/2152 and see how to do so