The goal of file upload attack is to abuse an upload function that is not properly guarded to allow us to upload files that's outside the predefined scope of file types allowed, so that we can trigger the malicious file we uploaded, either via interacting with the website / LFI or other means

Identifying Web Framework:

Usually, before we perform file upload attack, what we want to do first is to identify the underlying web framework that's in use, with this piece of information, we can craft payloads that would work on our victim (webshell / rev shell etc.)

To identify web framework, the most simple way is to visit index.ext page, the extension would tell us about what's the underlying framework, we can also refer to [[Fuzzing with ffuf]] to fuzz for extension if needed.

Aside from fuzzing, we can also attempt to use Wappalyzer https://www.wappalyzer.com/, Wappalyzer can provide us an overview with the underlying framework and other information (OS, Web Servers etc.)

Client-Side Verification:

Client-Side Verification equals to no verification, as we can simply edit the code or intercept the response before it hit our browser, refer to https://academy.hackthebox.com/app/module/136/section/1280 to look back at technique to bypass

Bypassing Blacklist Filter:
