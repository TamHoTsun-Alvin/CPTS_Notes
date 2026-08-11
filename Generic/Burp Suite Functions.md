-Automatic Modification:

Using Burp-Suite, we can have it to match regex and automatically modify all outgoing or incoming HTTP request / responses

`Proxy>Proxy settings>HTTP match and replace rules>Add`

Set respective options and use regex to match

-Repeating Request

We can repeat a single request / modify based on a request by sending a specific request to the repeater, we can also observe the response after modification

`Proxy>HTTP History>right click request>Send to Repeater`

-Decoding:

At 