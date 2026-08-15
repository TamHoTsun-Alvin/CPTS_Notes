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

Bypassing Encoded References:

We can attempt to bypass encoded reference if we are able to know what scheme is used in encoding, although we can't know the original content, but we can hash different values into the used scheme and see if we can attempt for mass enumeration

Function Disclosure:

Sometimes, we might have access to source code and see how is the encoded reference being created, for example, if we can reveal how is the encoded reference being created, then we can recreate the reference or even attempt to reveal what was the original value, Consider the following:
```
function downloadContract(uid) {
    $.redirect("/download.php", {
        contract: CryptoJS.MD5(btoa(uid)).toString(),
    }, "POST", "_self");
}
```

With the following function, we can know that the original provided parameter is uid, and it got converted into base64 then md5.

IDOR in Insecure API:

We can also attempt to test for IDOR in API's, if IDOR vulnerability exists, we can access other user's information / archive privilege escalation by simply providing a different parameters