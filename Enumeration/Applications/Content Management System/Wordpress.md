Wordpress is a very common and famous CMS used to host blogs, forums and website, the high presence and available of bugs of it makes it a great attacking vector for us

Enumerating Wordpress:

There exist multiple way to identify wordpress, the quickest way is to use wappalyzer, aside from it, there are few evidence that wordpress is in use:

-The presence of the string WordPress
-The presence of a theme from WordPress
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

We can enumerate wps automatically using wpscan