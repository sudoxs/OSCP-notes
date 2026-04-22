dirsearch -u (...)

---
```
gobuster dir -u http://192.168.126.50 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt -k -t 50 -x pdf,txt
```

---
```
gobuster dir -u http://X.X.X.X:xxx -w /usr/share/wordlists/dirb/big.txt 
```
```
gobuster vhost -u http://X.X.X.X:xxx -w SecLists/Discovery/DNS/subdomains-top1million-5000.txt
```
gobuster dns -u ... -w ...

-p pattern
{GOBUSTER}/v1
{GOBUSTER}/v2
/api_name/v1

--add-slash
-t 5
-q
-x php,html,txt,js,conf,backup,bak
-k 
-s "200,301,302"
-b "404,403"
--exclude-length 0
--append-domain
--force

|فلگ|توضیح|
|---|---|
|`-u`|URL هدف|
|`-w`|wordlist|
|`-t`|تعداد thread (مثلاً 50 برای سرعت)|
|`-x`|پسوند فایل (php,txt,html)|
|`-s`|فقط status code خاص (مثلاً 200,301)|
|`-b`|blacklist status (مثلاً 404)|
|`-o`|ذخیره خروجی|
|`-k`|ignore SSL|

---

403 = Forbidden
/.hta                 (Status: 403)
/.htaccess            (Status: 403)
/.htpasswd            (Status: 403)

301 = redirect
/css       (Status: 301)
/db        (Status: 301)
/images    (Status: 301)
/js        (Status: 301)
/uploads   (Status: 301)

---

SecLists/raw/master/Discovery/Web-Content/common.txt
SecLists/Discovery/DNS/subdomains-top1million-5000.txt
SecLists/Discovery/Web-Content/common.txt 

## Wordpress:
/wp-admin
/wp-admin.php
/wp-admin/admin.php?page=visitors-app%2Fadmin%2Fstart.php

etc/passwd
home/USER/.ssh/id_rsa
C:\Windows\System32\drivers\etc\hosts

## LFI:
SecLists/Fuzzing/LFI/LFI-gracefulsecurity-linux.txt
SecLists/Fuzzing/LFI/LFI-gracefulsecurity-windows.txt
SecLists/Fuzzing/LFI/LFI-Large.txt

## Wordpress:
SecLists/CMS/wordpress/wp-config.txt
SecLists/CMS/wordpress/wordpress-sensitive-files.txt

## IIS:
SecLists/Fuzzing/LFI/LFI-Windows.txt

## Apache:
SecLists/Discovery/Web-Content/Apache.txt
SecLists/Discovery/Web-Content/Linux.txt

## Traversal:
SecLists/Fuzzing/LFI/LFI-Jhaddix.txt

## Webshells:
ls -la /usr/share/webshells