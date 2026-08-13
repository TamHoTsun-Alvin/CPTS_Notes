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

This could be used to target other user if 