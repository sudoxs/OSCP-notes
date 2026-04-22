
| TLS 1.0 / TLS 1.1       | استفاده از پروتکل‌های قدیمی     | MITM / Downgrade Attack          |
| --------------------------- | ------------------------------------------ | -------------------------------- |
| CBC Cipher Suites       | الگوریتم رمزنگاری آسیب‌پذیر     | BEAST / POODLE                   |
| SHA1                    | الگوریتم هش شکسته شده           | Integrity Failure                |
| Weak Key Exchange       | Forward Secrecy ضعیف             | Session Compromise               |
| No HSTS                 | HTTPS اجباری نیست               | SSL Stripping                    |


| Missing Header        | به‌تنهایی Vulnerability است؟ | چه چیزی را نشان می‌دهد           | کاربرد در Pentest / Report  |
| --------------------- | --------------------------- | --------------------------- | -------------------------|
| CSP                   | خیر                         | Hardening ضعیف سمت وب    |             افزایش Impact XSS
| X-Frame-Options       | خیر      |               عدم دفاع در برابر UI Attack           | Clickjacking             
| HSTS                  | خیر                         | HTTPS به‌درستی enforce نشده   | SSL Stripping / Downgrade 
| Multiple Headers      | خیر           |             فرهنگ امنیتی ضعیف                | Security Misconfiguration 
