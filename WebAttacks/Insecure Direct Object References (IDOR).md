IDOR Vulnerability refers to a type of vulnerability that originates from a lack / weak access control system, the result of IDOR usually leads to information disclosure as user are able to access pages / information they should not be able to access, in an even worse case IDOR can lead to privilege escalation via IDOR Insecure Function Calls, assuming we have some limited function that's for admin only, but we have a weak access control system, leading to the fact that if a standard user knows the access point of parameter, he/she can simply make request to the respective API and the request won't be denied.

Identifying possible IDOR:

The first step in identifying IDOR is to identify Direct Object References, we should look for a specific 