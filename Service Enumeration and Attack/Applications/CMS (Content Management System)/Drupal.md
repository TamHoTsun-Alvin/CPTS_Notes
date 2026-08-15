Aside from wappalyzer, evidence below show us that Drupal is in use:

-The appearance of string Powered by Drupal in header or footer
```
curl -s http://drupal.inlanefreight.local | grep Drupal

<meta name="Generator" content="Drupal 8 (https://www.drupal.org)" />
      <span>Powered by <a href="https://www.drupal.org">Drupal</a></span>
```
-The appearance of the word node in robots.txt
-The presence of a CHANGELOG.txt or README.txt
-The URL structure being .../node/`nodeid`

Enumeration:

If we are not blocked access to, curling changelog.txt can see the version number:
```
curl -s http://drupal-acc.inlanefreight.local/CHANGELOG.txt | grep -m2 ""

Drupal 7.57, 2018-02-21
```

Once we ID the presence of Drupal, we can also use droopescan to automatically discover plugins, themes and other interesting stuff:
```
droopescan scan drupal -u <url>
```

Attacking Drupal:

Before Version 8, it is possible for us to login as admin (credentials needed) and enable the php filter module, which allows the server to evaluate embedded PHP code and snippets.

For detail, refer to https://academy.hackthebox.com/app/module/113/section/1209