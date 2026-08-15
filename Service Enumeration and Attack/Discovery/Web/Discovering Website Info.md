We can utilize whois to perform a whois lookup, which in return provide us information regarding a specific website

```
#Example in utilizing whois
whois inlanefreight.com
```

Utilizing Certificate Transparency Logs (CT Logs), we can reveal domains and subdomains related to a certain certificate
```
curl -s "https://crt.sh/?q=<website_name>&output=json" | jq -r '.[]
| select(.name_value | contains("dev")) | .name_value' | sort -u
#Example
curl -s "https://crt.sh/?q=inlanefreight.com&output=json" | jq -r '.[] | select(.name_value | contains("dev")) | .name_value' | sort -u
```

By analyzing a website's banner, we may reveal underlying technology / infrastructure to allow us better prepare for an attack
```
curl -I <domain_name>
#Example
curl -I https://inlanefreight.com
#Result
HTTP/1.1 301 Moved Permanently
Date: Fri, 31 May 2024 12:12:12 GMT
Server: Apache/2.4.41 (Ubuntu)
X-Redirect-By: WordPress
Location: https://www.inlanefreight.com/
Content-Type: text/html; charset=UTF-8
```

We can use wafw00f to check if a website is behind some sort of WAF:
```
wafw00f <domain_name>
#Example
wafw00f inlanefreight.com
```

We can check robots.txt to reveal existing directory for the website:
```
#Example
User-agent: *
Disallow: /admin/
Disallow: /private/
Allow: /public/

User-agent: Googlebot
Crawl-delay: 10

Sitemap: https://www.example.com/sitemap.xml
```

Ref: https://academy.hackthebox.com/app/module/144/section/1248 / https://academy.hackthebox.com/app/module/144/section/3077 / https://academy.hackthebox.com/app/module/144/section/3075

Ref for using Nessus: https://academy.hackthebox.com/app/module/108/section/1029