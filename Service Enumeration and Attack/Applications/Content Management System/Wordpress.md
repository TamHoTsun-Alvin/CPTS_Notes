Wordpress is a very common and famous CMS used to host blogs, forums and website, the high presence and available of bugs of it makes it a great attacking vector for us

Enumerating Wordpress:

There exist multiple way to identify wordpress, the quickest way is to use wappalyzer, aside from it, there are few evidence that wordpress is in use:

-The presence of the string WordPress
-The presence of a theme from WordPress
-The presence of wp-xxxx in robots.txt
-The presence of /wp-admin, /wp-content and wp-login page

Using cURL, we can enumerate the existence of WordPress, themes and plugin in use:

Confirming Existence of WordPress:
```
Avalon112@htb[/htb]$ curl -s http://blog.inlanefreight.local | grep WordPress

<meta name="generator" content="WordPress 5.8" /
```

Confirming Theme in use:
```
Avalon112@htb[/htb]$ curl -s http://blog.inlanefreight.local/ | grep themes

<link rel='stylesheet' id='bootstrap-css'  href='http://blog.inlanefreight.local/wp-content/themes/business-gravity/assets/vendors/bootstrap/css/bootstrap.min.css' type='text/css' media='all' />
```

Confirming Plugins in use:
```
Avalon112@htb[/htb]$ curl -s http://blog.inlanefreight.local/ | grep plugins

<link rel='stylesheet' id='contact-form-7-css'  href='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/css/styles.css?ver=5.4.2' type='text/css' media='all' />
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/subscriber.js?ver=5.8' id='subscriber-js-js'></script>
<..snip..>
```

User Enumeration:

Due to unique response on the wp-login page, it is possible to enumerate username on WordPress, notice that:

-`The username is not registered....` implies user does not exist

Otherwise, the user exist, just that our password is not correct.

Automated Enumeration:

We can enumerate wps automatically using wpscan like in the following example:
```
sudo wpscan --url <url> --enumerate --api-token <apifrom_https://wpscan.com/profile/>
```

Notes for creator only: A free account has already been registered at WPVulnDB, don't be lazy and grab that api token when in need

This tool would enumerate plugins, themes, users, version etc. and help us check for vulnerability related to installed asset, however the vulnerability checking would only work if we supply a api-token, of course, no api-token won't render the tool useless, we can still perform basic enumeration with it.

Attacking WordPress:

-Login Bruteforcing:

We can use WPScan for login bruteforcing in the following syntax:
```
sudo wpscan --password-attack <xmlrpc/wp-login> -t <threadcount> -U <username/userfile> -P <pathtowordlist> --url <url>
```

-Shell Uploading:

If we obtained admin access to wordpress, we can then use wp_admin_shell_upload module from Metasploit to upload a shell and execute automatically:
```
msf6 > use exploit/unix/webapp/wp_admin_shell_upload 
```

There is also way to perform this manually, refer to https://academy.hackthebox.com/app/module/113/section/1208 for more detail.