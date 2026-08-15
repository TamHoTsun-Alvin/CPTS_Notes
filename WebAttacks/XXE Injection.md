XML is a common markup language desinged for flexible transfer and storage of data and documents, however, when a user-controlled input is not properly sanitized of parsed, as a result XXE Injection would allow us to perform malicious action

Local File Disclosure with XXE:

First, we need to find a webpage that actually accepts XML user input, this can be verified in burp suite whenever we attempt to send data to webserver and intercept with burp, we also need to note for which element is being displayed to us, else we can't disclose files

After locating it, we can add a new entity inside to see if it indeed display what we typed in like in the following example (assuming email got displayed):
```
<!DOCTYPE email [
  <!ENTITY company "Inlane Freight">
]>
```
