-Automatic Modification:

Using Burp-Suite, we can have it to match regex and automatically modify all outgoing or incoming HTTP request / responses

`Proxy>Proxy settings>HTTP match and replace rules>Add`

Set respective options and use regex to match

-Repeating Request

We can repeat a single request / modify based on a request by sending a specific request to the repeater, we can also observe the response after modification

`Proxy>HTTP History>right click request>Send to Repeater`

-Decoding:

At the Decoder page, we can freely encode or decode strings into different format

-Proxy:

Aside from booting the built in browser, burp also provides a proxy that would allow us to intercept request and modify them with other browser we wish, by default the proxy is open at 127.0.0.1:8080

-Burp Fuzzer (Intruder):

Burp also have a Fuzzer called Burp intruder, however due to we using limited CE Edition, this Fuzzer should be considered last resort as it only support 1 request per second, prioritize other options like ffuf, gobuster before considering the use of Burp Fuzzer

We first select our base from the HTTP History and send the request we wish to the intruder (like how we sending request to repeater)

After we are done, we need to choose positions to fuzz, an example would be:

https://watermelonhamigua/secure/api/v2/getUserDetails/get.php?id=`fuzz`

We can choose Payload Type and Position, then, after that, we can start fuzzing and see what kind of response the webserver gave us

Note: Burp do have an active scanner but, not available at the CE Edition, therefore if need an active scanner, Ref to https://academy.hackthebox.com/app/module/110/section/1086 for using ZAP Scanner

Ref: https://academy.hackthebox.com/app/module/110