```
ffuf -u "http://X.X.X.X/FUZZ" -w ./common.txt
```

```
ffuf -u "http://target.com/page.php?id=FUZZ" -w payloads.txt -mc 200
```
```
ffuf -u http://target.com/login -X POST -d "user=admin&pass=FUZZ" -w passwords.txt
```

FUZZ: کلمه کلیدی که جایگزین می‌شود
-fs: فیلتر اندازه پاسخ (Filter Size) 1042,1088
-mc: فیلتر کد وضعیت (Filter Status Code) 200,301,302,403
-e .php
-H "User-Agent: Mozilla/5.0"
-c # red
-t 20
-fr "404|Not Found"

-u "http://X.X.X.X/FUZ1/FUZ2" -w ./common.txt:FUZ1 -w ./common.txt:FUZ2

|فلگ|توضیح|
|---|---|
|`-u`|URL (با FUZZ)|
|`-w`|wordlist|
|`-X`|متد (GET, POST)|
|`-d`|داده POST|
|`-H`|header|
|`-mc`|match status code|
|`-fc`|filter status code|
|`-fs`|filter size (خیلی مهم برای حذف false positive)|
|`-fw`|filter word count|
|`-t`|thread|
|`-o`|خروجی|
|`-of`|فرمت خروجی (json)|

---
SecLists/raw/master/Discovery/Web-Content/common.txt
SecLists/Discovery/DNS/subdomains-top1million-5000.txt
SecLists/Discovery/Web-Content/common.txt 

Wordpress:
/wp-admin
/wp-admin.php
/wp-admin/admin.php?page=visitors-app%2Fadmin%2Fstart.php

etc/passwd
home/USER/.ssh/id_rsa
C:\Windows\System32\drivers\etc\hosts

LFI:
SecLists/Fuzzing/LFI/LFI-gracefulsecurity-linux.txt
SecLists/Fuzzing/LFI/LFI-gracefulsecurity-windows.txt
SecLists/Fuzzing/LFI/LFI-Large.txt

Wordpress:
SecLists/CMS/wordpress/wp-config.txt
SecLists/CMS/wordpress/wordpress-sensitive-files.txt

IIS:
SecLists/Fuzzing/LFI/LFI-Windows.txt

Apache:
SecLists/Discovery/Web-Content/Apache.txt
SecLists/Discovery/Web-Content/Linux.txt

Traversal:
SecLists/Fuzzing/LFI/LFI-Jhaddix.txt

Webshells:
ls -la /usr/share/webshells
