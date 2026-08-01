Those method require the target have respective coding language installed, and thus is considered second line method

Python2 - Download:
```
python2.7 -c 'import urllib;urllib.urlretrieve ("<url_to_file>", "<filename_to_be_saved>")'
```
Python 3 - Download:
```
python3 -c 'import urllib.request;urllib.request.urlretrieve("<url_to_file>", "<filename_to_be_saved>")'
```
PHP:
```
php -r '$file = file_get_contents("<url_to_file"); file_put_contents("<filename_to_be_Saved>",$file);'
```

For other languages, Ref: https://academy.hackthebox.com/app/module/24/section/1574