During testing, whenever we have access to a system, we maybe able to get ourselves a easy win when we can find credentials for other users in the system, even though the credentials we found is not useful now it is still worth it, as maybe we can use it to access other systems.

Searching credentials in file:

Often we can find passwords or credentials stored in cleartext config files or note file, sometimes this might be the doing of an installed application or a user that afraid he/she forgets the password, we can use findstr to search for such sensitive information:
```
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml
```
```
findstr /spin "password" *.*
```
```
findstr /si password *.xml *.ini *.txt *.config
```

We can replace password with other keyword as well, like pass, admin or other keyword we wish to find

We can also search with powershell:
```
PS C:\htb> select-string -Path C:\Users\htb-student\Documents\*.txt -Pattern password
```
```
dir /S /B *pass*.txt == *pass*.xml == *pass*.ini == *cred* == *vnc* == *.config*
```
```
where /R C:\ *.config
```
Search all files end in specific extension with powershell:
```
Get-ChildItem C:\ -Recurse -Include *.rdp, *.config, *.vnc, *.cred -ErrorAction Ignore
```

Dictionary Files and Unattended installation Files:

Sometimes, a user may accidentally add a password to Dictionary or intentionally to avoid the red underlines, also credentials are likely to be included in the unattended installation xml files:
```
PS C:\htb> gc 'C:\Users\htb-student\AppData\Local\Google\Chrome\User Data\Default\Custom Dictionary.txt' | Select-String password
```

```
#Example Unattended.xml
<?xml version="1.0" encoding="utf-8"?>
<unattend xmlns="urn:schemas-microsoft-com:unattend">
    <settings pass="specialize">
        <component name="Microsoft-Windows-Shell-Setup" processorArchitecture="amd64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <AutoLogon>
                <Password>
                    <Value>local_4dmin_p@ss</Value>
                    <PlainText>true</PlainText>
                </Password>
                <Enabled>true</Enabled>
                <LogonCount>2</LogonCount>
                <Username>Administrator</Username>
            </AutoLogon>
            <ComputerName>*</ComputerName>
        </component>
    </settings>
```

Enumerate PowerShell History:

Many PS commands allows user to pass credentials on the command line, starting with Powershell 5.0 powershells start store command history at 
`C:\Users\<username>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`

We can first confirm History Save Path:
```
PS C:\htb> (Get-PSReadLineOption).HistorySavePath
```

Then we can attempt to read it:
```
PS C:\htb> gc (Get-PSReadLineOption).HistorySavePath
```

This one liner allows us to retrieve the content of all PowerShell History Files:
```
foreach($user in ((ls C:\users).fullname)){cat "$user\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt" -ErrorAction SilentlyContinue}
```

Sometimes, powershell credentials are stored in a way that is encrypted, such credentials are usually protected using DPAPI, we we gained access to the specific user created that encrypted file or command execution on that user's syntax, we can decrypt such password:
```
# Connect-VC.ps1
# Get-Credential | Export-Clixml -Path 'C:\scripts\pass.xml'
$encryptedPassword = Import-Clixml -Path 'C:\scripts\pass.xml'
$decryptedPassword = $encryptedPassword.GetNetworkCredential().Password
Connect-VIServer -Server 'VC-01' -User 'bob_adm' -Password $decryptedPassword
```

```
PS C:\htb> $credential = Import-Clixml -Path 'C:\scripts\pass.xml'
PS C:\htb> $credential.GetNetworkCredential().username

bob


PS C:\htb> $credential.GetNetworkCredential().password

Str0ng3ncryptedP@ss!
```


Searching Passwords in sticky notes:

Sometimes, people use sticky notes to save password and other information however that's just simply a sqlite DB file which is located at `C:\Users\<user>\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite`

```
PS C:\htb> ls
 
 
    Directory: C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState
 
 
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         5/25/2021  11:59 AM          20480 15cbbc93e90a4d56bf8d9a29305b8981.storage.session
-a----         5/25/2021  11:59 AM            982 Ecs.dat
-a----         5/25/2021  11:59 AM           4096 plum.sqlite
-a----         5/25/2021  11:59 AM          32768 plum.sqlite-shm
-a----         5/25/2021  12:00 PM         197792 plum.sqlite-wal
```

We need to copy the all three plum.sqlite file to our system and use something like DB Browser for SQLite, alternatively we can use https://github.com/RamblingCookieMonster/PSSQLite this module to view the data inside.