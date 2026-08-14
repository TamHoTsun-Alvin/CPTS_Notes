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
We need to know how each of them will execute command 