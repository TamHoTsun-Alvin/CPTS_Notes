The following evidence would tell us that Joomla is in use, aside from wappalyzer result:

-The presence of string Joomla in page source (usually near meta name generator)
-The presence of string Joomla in robots.txt
-The presence of Joomla favicon

Identifying Joomla Version:

If readme.txt is present, we can know what Joomla version is in use:
```
curl -s http://dev.inlanefreight.local/README.txt | head -n 5
```

If we are able to access js files or joomla.xml at administrator/manifests/files/joomla.xml, then we can know what Joomla version is in use:
```
curl -s http://dev.inlanefreight.local/administrator/manifests/files/joomla.xml | xmllint --format -
```

Automatic Enumeration:

We can use droopescan for our purpose:
```
droopescan scan <cms> -u <url>
#Example
droopescan scan joomla --url http://dev.inlanefreight.local/
```

Exploiting Joomla:

Similar to how we can upload a shell to WordPress, we can upload a shell to Joomla as well if we can have administrator access, refer to https://academy.hackthebox.com/app/module/113/section/1210 for more details

