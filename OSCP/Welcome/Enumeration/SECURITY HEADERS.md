
| Header                      | اگر وجود نداشته باشد یعنی                         | تمرکز تست / حمله                |
| --------------------------- | ------------------------------------------ | -------------------------------- |
| Content-Security-Policy     | هیچ محدودیتی روی اجرای JavaScript وجود ندارد      | XSS (Reflected / Stored / DOM)   |
| X-Frame-Options             | صفحه می‌تواند داخل iframe لود شود               | Clickjacking                     |
| X-Content-Type-Options      | MIME Sniffing فعال است                      | File Upload / XSS                |
| Referrer-Policy             | اطلاعات URL و Token ممکن است نشت کند         | Information Disclosure           |
| Strict-Transport-Security   | HTTPS اجباری نشده                            | SSL Downgrade / MITM             |
| Permissions-Policy          | APIهای مرورگر بدون محدودیت در دسترس هستند        | Browser Feature Abuse            |
