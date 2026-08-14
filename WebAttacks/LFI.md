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

In the above case, what we need to do is simply 