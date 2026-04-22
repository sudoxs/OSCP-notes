📊 جدول فیلترهای پرکاربرد Wireshark

| هدف          | فیلتر                         | معنی         |
| ------------ | ----------------------------- | ------------ |
| فقط HTTP     | `http`                        | ترافیک وب    |
| فقط HTTPS    | `tls`                         | SSL/TLS      |
| IP خاص       | `ip.addr == 192.168.1.10`     | رفت‌وبرگشت   |
| فقط مقصد     | `ip.dst == 8.8.8.8`           | خروجی        |
| فقط مبدا     | `ip.src == 10.0.0.5`          | ورودی        |
| پورت خاص     | `tcp.port == 443`             | HTTPS        |
| DNS          | `dns`                         | کوئری دامنه  |
| درخواست HTTP | `http.request`                | فقط request  |
| پاسخ HTTP    | `http.response`               | فقط response |
| خطاها        | `tcp.analysis.retransmission` | مشکل شبکه    |


📈 ابزارهای داخلی مهم Wireshark

| مسیر                       | کاربرد           |
| -------------------------- | ---------------- |
| Statistics → Conversations | کی با کی حرف زده |
| Statistics → Endpoints     | پرمصرف‌ترین IP   |
| Statistics → IO Graph      | الگوی زمانی      |
| Follow → TCP Stream        | بازسازی مکالمه   |
## 🧠 مثال ذهنی (مثل Mr. Robot)

- Conversations → یک IP با ترافیک بالا
    
- IO Graph → خط صاف و پایدار
    
- نتیجه:
    

> «اینجا استریم یا سروره»

