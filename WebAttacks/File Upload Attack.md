The goal of file upload attack is to abuse an upload function that is not properly guarded to allow us to upload files that's outside the predefined scope of file types allowed, so that we can trigger the malicious file we uploaded, either via interacting with the website / LFI or other means

Identifying Web Framework:

Usually, before we perform file upload attack, what we want to do first is to identify the underlying web framework that's in use, with this piece of information, we can craft payloads that would work on our victim (webshell / rev shell etc.)

To identify web framework, the most simple way is to visit index.ext page, the extension would tell us about what's the underlying framework, we can also refer to [[Fuzzing with ffuf]] to fuzz for extension if needed.

Aside from fuzzing, we can also attempt to use Wappalyzer https://www.wappalyzer.com/, Wappalyzer can provide us an overview with the underlying framework and other information (OS, Web Servers etc.)

Client-Side Verification:

Client-Side Verification equals to no verification, as we can simply edit the code or intercept the response before it hit our browser, refer to https://academy.hackthebox.com/app/module/136/section/1280 to look back at technique to bypass

Bypassing Blacklist Filter:

To bypass a blacklist filter, we first need to know what is actually blocked, but instead of using ffuf, we are needed to use burp intruder instead, as we are interested in the blacklist filter, we first select a valid files, then without modifying the file content, we specify the extension as our fuzzing position, then we use wordlist like https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt seclist web extension list or https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Extension%20PHP/extensions.lst this php list to fuzz, in this way we can get a list of allowed extension that we can upload.

Bypassing White Filter:

Bypassing a White Filter is more troublesome then a blacklist filter, as depending on how the filter is written, we might be finding ourselves to be blocked / limited by the filter a lot, below is a table of method that we can try to use:


| Method Name              | Example        | Description                                                                                                                                                                                                                                                            |
| ------------------------ | -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Double Extensions        | apple.jpg.php  | Able to bypass simple filter that doesn't care if file end in permitted extension                                                                                                                                                                                      |
| Reverse double Extension | apple.php.jpeg | Able to bypass filter case that care file actually ends in whitelisted extension but internal config treat all file containing a certain extension to be executable (example: only check if contain .php but not end in .php)                                          |
| Character Injection      | apple.php%00   | We can attempt to inject specific characters before or after the final extension to cause the webapp to misinterpret the uploaded file as a php script, below is the list:<br>- `%20`<br>- `%0a`<br>- `%00`<br>- `%0d0a`<br>- `/`<br>- `.\`<br>- `.`<br>- `…`<br>- `:` |
|                          |                |                                                                                                                                                                                                                                                                        |
