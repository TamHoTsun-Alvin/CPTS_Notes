During testing, whenever we have access to a system, we maybe able to get ourselves a easy win when we can find credentials for other users in the system, even though the credentials we found is not useful now it is still worth it, as maybe we can use it to access other systems.

Searching credentials in file:

Often we can find passwords or credentials stored in cleartext config files or note file, sometimes this might be the doing of an installed application or a user that afraid he/she forgets the password, we can use findstr to search for such sensitive information:
```
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml
```

We can replace password with other keyword as well, like pass, admin or other keyword we wish to find

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