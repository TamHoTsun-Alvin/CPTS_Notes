Tomcat is a webserver that enable the hosting of applications written in Java, while less exposed to internet, it is an excellent foothold for us to make if we can access it.

Discovery of Tomcat:

The discovery of tomcat can be done by using eyewitness or nmap, if we suspect a server is hosting tomcat, we can check it out by visiting a non-valid page (Example: https://app-dev.inlanefreight.local:8080/gugugaga), if Tomcat is behind, an error page would be presented to us with the String Apache Tomcat along with version number, we can also try curling the docs page:
```
curl -s http://app-dev.inlanefreight.local:8080/docs/ | grep Tomcat 

<html lang="en"><head><META http-equiv="Content-Type" content="text/html; charset=UTF-8"><link href="./images/docs-stylesheet.css" rel="stylesheet" type="text/css"><title>Apache Tomcat 9 (9.0.30) - Documentation Index</title><meta name="author" 

<SNIP>
```