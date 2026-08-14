The goal of file upload attack is to abuse an upload function that is not properly guarded to allow us to upload files that's outside the predefined scope of file types allowed, so that we can trigger the malicious file we uploaded, either via interacting with the website / LFI or other means

Identifying Web Framework:

Usually, before we perform file upload attack, what we want to do first is to identify the underlying web framework that's in use, with this piece of information, we can craft payloads that would work on our victim (webshell / rev shell etc.)