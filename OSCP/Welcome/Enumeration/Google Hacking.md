firefox --search "Microsoft Edge site:exploit-db.com"

| Operator   | مثال                      | کاربرد                          |
| ---------- | ------------------------- | ------------------------------- |
| `site:`    | `site:exploit-db.com`     | محدود کردن نتیجه به یک سایت خاص |
| `inurl:`   | `inurl:php?id=`           | پیدا کردن URLهای آسیب‌پذیر      |
| `intitle:` | `intitle:"index of"`      | هدف قرار دادن عنوان صفحه        |
| `intext:`  | `intext:"SQL injection"`  | جستجو در متن صفحه               |
| `" "`      | `"WordPress 5.2 exploit"` | جستجو دقیق عبارتی مشخص          |


| Operator      | مثال                             | کاربرد                                 |
| ------------- | -------------------------------- | -------------------------------------- |
| `site:`       | `site:exploit-db.com`            | محدود کردن نتیجه به یک سایت خاص        |
| `inurl:`      | `inurl:php?id=`                  | پیدا کردن URLهای آسیب‌پذیر             |
| `intitle:`    | `intitle:"index of"`             | هدف قرار دادن عنوان صفحه               |
| `intext:`     | `intext:"SQL injection"`         | جستجو در متن صفحه                      |
| `" "`         | `"WordPress 5.2 exploit"`        | جستجوی دقیق عبارتی مشخص                |
| `filetype:`   | `filetype:pdf "buffer overflow"` | جستجوی فایل با فرمت مشخص               |
| `ext:`        | `ext:nse ssh`                    | جستجوی فایل با پسوند مشخص              |
| `allintitle:` | `allintitle:admin login`         | جستجوی صفحات با تمام کلمات در عنوان    |
| `allinurl:`   | `allinurl:admin panel`           | جستجوی URLهای شامل تمام کلمات          |
| `allintext:`  | `allintext:"password leak"`      | جستجوی صفحات شامل تمام کلمات در متن    |
| `cache:`      | `cache:example.com`              | مشاهده نسخه کش شده سایت                |
| `related:`    | `related:example.com`            | پیدا کردن سایت‌های مشابه               |
| `inanchor:`   | `inanchor:"click here"`          | جستجو بر اساس متن لینک‌ها              |
| `AROUND(X)`   | `"sql injection" AROUND(5) php`  | پیدا کردن عباراتی که به هم نزدیک هستند |
| `-`           | `php -tutorial`                  | حذف نتایج شامل کلمه مشخص               |
| `+`           | `+"remote code execution"`       | اطمینان از حضور کلمه در نتایج          |
| `*`           | `"WordPress * exploit"`          | جایگزینی wildcard برای هر کلمه         |
| `define:`     | `define:buffer overflow`         | تعریف یک کلمه در سایت‌ها یا دیکشنری‌ها |
| `info:`       | `info:exploit-db.com`            | نمایش اطلاعات سایت                     |
| `link:`       | `link:example.com`               | نمایش صفحات لینک شده به سایت           |
