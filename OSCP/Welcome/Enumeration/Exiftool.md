```
exiftool -a -u brochure.pdf
```
```bash
exiftool -a -u -g1 file.pdf
```

 `-a` برای نمایش تگ‌های تکراری

 `-u` برای نمایش تگ‌های ناشناخته



|فیلد|چرا مهمه|
|---|---|
|Author|اسم کاربر سیستم|
|Creator|نرم‌افزار (مثلاً Word → احتمال macro)|
|Producer|PDF generator|
|CreationDate|timeline|
|ModDate|آخرین تغییر|
|Title|اطلاعات پروژه|
|Embedded Files|ممکنه فایل مخفی داشته باشه|
|File Path|مسیر لو رفته از سیستم|

### edit:
```bash
exiftool -Tag="value" file
```
	exiftool -Author="admin" -Title="confidential" -Creator="Word" file.pdf
del tag:
```bash
exiftool -Author= file.pdf
```
del all:
```bash
exiftool -all= file.pdf
```
change without backup:
```bash
exiftool -overwrite_original -Author="test" file.pdf
```