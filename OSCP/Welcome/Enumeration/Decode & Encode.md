https://hashes.com/en/tools/hash_identifier

# فلگ‌های اصلی Hashcat

| Flag | Meaning | Description |
|------|--------|-------------|
| -m  | Hash mode | تعیین نوع هش (مثلاً 400 = WordPress phpass) |
| -a  | Attack mode | نوع حمله (Dictionary, Brute-force, Hybrid) |
| -o  | Output file | ذخیره هش‌های کرک‌شده در فایل |
| -O  | Optimized kernels | اجرای سریع‌تر با محدودیت طول پسورد |
| --force | Force run | اجبار اجرا (برای VM یا GPU ناسازگار) |
| --show | Show cracked | نمایش هش‌های کرک‌شده |
| --status | Live status | نمایش وضعیت زنده |
| --status-timer | Status interval | فاصله زمانی نمایش وضعیت |
# Attack Mode‌ها (`-a`)

| Mode | Name | Description | Example |
|------|------|-------------|---------|
| 0 | Straight | دیکشنری ساده | -a 0 rockyou.txt |
| 1 | Combinator | ترکیب دو wordlist | -a 1 list1 list2 |
| 3 | Brute-force | brute-force کامل | -a 3 ?l?l?l?l |
| 6 | Hybrid WL + Mask | کلمه + الگو | -a 6 rockyou.txt ?d?d |
| 7 | Hybrid Mask + WL | الگو + کلمه | -a 7 ?u?l rockyou.txt |
# Hash Mode‌های رایج (OSCP / CTF)

| Mode | Hash Type | Usage |
|------|----------|-------|
| 0 | MD5 | قدیمی، ناامن |
| 100 | SHA1 | قدیمی |
| 400 | phpass | WordPress |
| 500 | MD5crypt | Linux |
| 1800 | SHA512crypt | Linux |
| 3200 | bcrypt | Web apps |
| 2611 | vBulletin | Forum |

| Scenario | Command |
|---------|---------|
| WordPress basic | hashcat -m 400 -a 0 hash.txt rockyou.txt |
| With rules | hashcat -m 400 -a 0 hash.txt rockyou.txt -r best64.rule |
| Hybrid | hashcat -m 400 -a 6 hash.txt rockyou.txt ?d?d |
| Show result | hashcat -m 400 --show hash.txt |
