IDOR Vulnerability refers to a type of vulnerability that originates from a lack / weak access control system, the result of IDOR usually leads to information disclosure as user are able to access pages / information they should not be able to access, in an even worse case IDOR can lead to privilege escalation via IDOR Insecure Function Calls, assuming we have some limited function that's for admin only, but we have a weak access control system, leading to the fact that if a standard user knows the access point of parameter, he/she can simply make request to the respective API and the request won't be denied.

Identifying possible IDOR:

The first step in identifying IDOR is to identify Direct Object References, we should look for a url parameters within an HTTP Request to for signs of direct object reference (Example: ?uid=1, ?filename=apple.jpg), the most simple test is to changing the names of incrementing id values to see if we can gain access to files that we are not supposed to have access, any successful file read would indicate an IDOR vulnerability

IDOR in ajax call:

Sometimes, even as non admin user, we might be able to locate ajax calls or api to handle admin function in the webpage, if we are able to locate them we can attempt to call it and see if it would still accept our request

Hashed Direct object reference:

Sometimes, the direct object reference maybe hashed / encoded when it is used in the url, sometimes we can guess / use burp decoder to decode the encoded reference, sometimes we would need to see the source code before we are able to know how is it encoded.

Insecure Parameters:

Let's assume a website let us access documents that we previously uploaded:
```
https://yolodrive/udoc.php?uid=21800000
```

As we can see, this webpage passed our uid as parameters, if we change our uid to a different value like:
```
https://yolodrive/udoc.php?uid=11100000
```

If we are able to see files for user with uid 11100000, then it means we sucessfully exploited the IDOR Vulnerability, for mass enumeration, we can use ffuf or burp suite to fuzz for different UIDs