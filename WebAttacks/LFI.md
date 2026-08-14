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