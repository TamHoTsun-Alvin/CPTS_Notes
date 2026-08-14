HTTP Protocols works in a way that it accepts different predefined verb at the beginning of an http request to help developers to develop different procedures to handle request differentiated by verbs, usually each verb has it's own usage (GET for getting resource, PUT for writing information) but it's up to developers implementation

A total of 9 verb is available and is listed below:

|Method|Safe|Idempotent|Cacheable|
|---|---|---|---|
|[`GET`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/GET)|Yes|Yes|Yes|
|[`HEAD`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/HEAD)|Yes|Yes|Yes|
|[`OPTIONS`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/OPTIONS)|Yes|Yes|No|
|[`TRACE`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/TRACE)|Yes|Yes|No|
|[`PUT`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/PUT)|No|Yes|No|
|[`DELETE`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/DELETE)|No|Yes|No|
|[`POST`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/POST)|No|No|Conditional*|
|[`PATCH`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/PATCH)|No|No|Conditional*|
|[`CONNECT`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/CONNECT)|No|No|No|
Yet, due to the amount of verb available, if a page is designed with intention to serve get and post request, even though proper security mechanism or filter is in place, their is a chance that such mechanism does not apply when we change the verb to something like PUT or CONNECT

Enumerating methods accepted by a server:

To see what kind of request a server is accepting, we can send an OPTION request to it and see what kind of methods is allowed:
```
curl -i -X OPTIONS http://SERVER_IP:PORT/
```

After that, we can start testing out if sending a request in any of the accepted method will result in security bypassing

Bypassing Basic Auth and Security Filters:

If some pages of the server are protected by basic auths, there is a chance that we can bypass it if we change our request methods, this vulnerability is caused by Insecure Web Server Configurations

During the development of webpage, frequently it would be deemed necessary to have some security mechanism deployed (like a filter to block malicious request), however sometimes if such filters does not cover all HTTP methods supported in certain functionalities, then we would be able to bypass security filters using HTTP Verb Tempering