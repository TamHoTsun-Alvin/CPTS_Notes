Command Injections refers to a type of vulnerability that allows us to execute a command in a way that is caused by input controlled by user is mistreated as code to be executed

The following are some of the most common types of injections:

|Injection|Description|
|---|---|
|OS Command Injection|Occurs when user input is directly used as part of an OS command.|
|Code Injection|Occurs when user input is directly within a function that evaluates code.|
|SQL Injections|Occurs when user input is directly used as part of an SQL query.|
|Cross-Site Scripting/HTML Injection|Occurs when exact user input is displayed on a web page.|

Detection:

We first would need to see a function that we can reasonably guess that a system command is executed with parameters we entered (For Example: Ping checker, which obviously takes IP address we enter then execute a ping command), if we are able to observe a change in output behavior after we perform command injection, it means that we have successfully exploited the vulnerability.

Command Injection Methods:

Below is a table of operators that can be used for command injection:

|**Injection Operator**|**Injection Character**|**URL-Encoded Character**|**Executed Command**|
|---|---|---|---|
|Semicolon|`;`|`%3b`|Both|
|New Line|`\n`|`%0a`|Both|
|Background|`&`|`%26`|Both (second output generally shown first)|
|Pipe|`\|`|`%7c`|Both (only second output is shown)|
|AND|`&&`|`%26%26`|Both (only if first succeeds)|
|OR|`\|`|`%7c%7c`|Second (only if first fails)|
|Sub-Shell|` `` `|`%60%60`|Both **(Linux-only)**|
|Sub-Shell|`$()`|`%24%28%29`|Both **(Linux-only)**|
We need to know how each of them will perform command injection as some of them might get blocked or filtered out, more often is not up to our choice that what can be used.

Injecting Commands:

The basic of Command Injection is to use the operators above to terminate the current commands and input our commands back, for example:

```
ping -c 1 <userinput> #Example: ping -c 1 10.10.16.172
#However if the userinput is 10.10.16.172; pwd
ping -c 1 10.10.16.172; pwd
```

We can check if our payload is valid by typing command in our terminal

Usually, for user convenience, modern website will have front-end verification to assist user in using the website correctly, however in the eyes of us that equals to no verification, as with the help of burp repeater or zap, such can be easily intercepted and changed.

Depending on different type of injection, we can use different operators:

|**Injection Type**|**Operators**|
|---|---|
|SQL Injection|`'` `,` `;` `--` `/* */`|
|Command Injection|`;` `&&`|
|LDAP Injection|`*` `(` `)` `&` `\|`|
|XPath Injection|`'` `or` `and` `not` `substring` `concat` `count`|
|OS Command Injection|`;` `&` `\|`|
|Code Injection|`'` `;` `--` `/* */` `$()` `${}` `#{}` `%{}` `^`|
|Directory Traversal/File Path Traversal|`../` `..\\` `%00`|
|Object Injection|`;` `&` `\|`|
|XQuery Injection|`'` `;` `--` `/* */`|
|Shellcode Injection|`\x` `\u` `%u` `%n`|
|Header Injection|`\n` `\r\n` `\t` `%0d` `%0a` `%09`|
