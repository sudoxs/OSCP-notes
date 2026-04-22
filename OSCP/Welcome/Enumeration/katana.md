```
katana -u https://target.com -depth 5 -jc -kf all -silent -o katana.txt
```

cat katana.txt | sort -u > clean.txt
grep "=" clean.txt > params.txt
grep -Ev "\.(js|css|png|jpg|jpeg|gif|svg|ico|woff|woff2|ttf|map)$" params.txt > params2.txt
grep -Ev "\.(js|css|png|jpg|jpeg|gif|svg|ico|woff|woff2|ttf|map)$" params2.txt > params3.txt

grep -E '\?.+=' params3.txt > final.txt
grep -Ei "id=|uid=|user=|item=|cat=|pid=" params3.txt > final.txt
wc -l final.txt


| فلگ | چرا مهم است |
|----|-------------|
| -u | بدون آن اجرا نمی‌شود |
| -depth | کنترل عمق و نویز |
| -jc | کشف endpointهای واقعی |
| -kf all | پیدا کردن ورودی‌ها |
| -silent | خروجی تمیز برای ابزار بعدی |
| -o | automation و اسکن |

| فلگ | پارامتر | توضیح | برای چه کاری استفاده می‌شود |
|----|--------|-------|-----------------------------|
| -u | URL | مشخص کردن آدرس هدف | نقطه شروع crawl |
| -list | فایل | لیست URLها | crawl چند هدف همزمان |
| -depth | عدد | عمق crawl | کشف مسیرهای تو در تو |
| -jc | — | JavaScript Crawl | استخراج endpoint از JS |
| -jsl | — | JS Link Finder | لینک‌های داخل JS |
| -kf | all / forms | استخراج فرم‌ها | پیدا کردن ورودی‌های کاربر |
| -silent | — | خروجی تمیز | استفاده در pipeline |
| -o | فایل | ذخیره خروجی | feed به اسکنر یا تحلیل |
| -c | عدد | تعداد کانکشن | افزایش سرعت crawl |
| -rate-limit | عدد | محدودیت درخواست | جلوگیری از بلاک |
| -timeout | ثانیه | تایم‌اوت | سایت‌های کند |
| -H | هدر | هدر سفارشی | Authorization / Cookie |
| -proxy | آدرس | استفاده از proxy | عبور از محدودیت یا Burp |
| -include | regex | فقط این URLها | فیلتر خروجی |
| -exclude | regex | حذف این URLها | کاهش نویز |
| -aff | — | Automatic Form Fill | تست فرم‌ها |
| -fx | — | Follow Redirects | دنبال کردن ریدایرکت‌ها |
instalation:[^1]

[^1]: instalation:
	
	go install github.com/projectdiscovery/katana/cmd/katana@latest
	
	
	export PATH=$PATH:~/go/bin
	
