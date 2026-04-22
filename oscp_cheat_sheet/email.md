بر اساس تمام فایل‌های رسمی OffSec که فرستادی، اینجا **تمام نکات حیاتی روز آزمون OSCP+** رو به صورت چک‌لیست و دستورالعمل آماده کردم:

---

## 🚨 تغییرات جدید آزمون OSCP+ (از ۱ نوامبر ۲۰۲۴)

| تغییر | توضیح | تأثیر روی استراتژی |
|-------|-------|-------------------|
| **Assumed Breach** | دیگه از بیرون شروع نمی‌کنی! یه یوزر دامین (`Eric.Wallows`) و پسورد (`EricLikesRunning800`) از اول بهت میدن | مستقیماً برو سراغ AD Enumeration با این creds |
| **حذف Bonus Points** | دیگه خبری از ۱۰ امتیاز اضافی نیست. فقط ۱۰۰ امتیاز محض | حتماً باید ۷۰ امتیاز رو از ماشین‌ها بگیری. هیچ راه فراری نیست |
| **OSCP+ سه‌ساله** | گواهینامه + سه سال اعتبار داره (بعدش باید تمدید کنی) | OSCP پایه مادام‌العمره، ولی + باید تمدید شه |

---

## 📊 ساختار آزمون (۱۰۰ امتیاز → ۷۰ نیازه)

```
┌─────────────────────────────────────────────────────────────┐
│  ۳ Standalone Machine (۶۰ امتیاز)                          │
│  ├── هر کدام: ۱۰ pts (local.txt) + ۱۰ pts (proof.txt)     │
│  └── مستقل از هم و از AD                                   │
├─────────────────────────────────────────────────────────────┤
│  ۱ Active Directory Set (۴۰ امتیاز) = ۳ ماشین              │
│  ├── Machine 1: ۱۰ pts (با creds اولیه قابل دسترسی)       │
│  ├── Machine 2: ۱۰ pts (lateral movement)                  │
│  └── Machine 3: ۲۰ pts (معمولاً DC یا high-value target)  │
│      └── creds شروع: Eric.Wallows / EricLikesRunning800   │
└─────────────────────────────────────────────────────────────┘

ترکیب‌های قبولی برای ۷۰ امتیاز:
✅ ۴۰ AD + ۳ local.txt (۳×۱۰) = ۷۰
✅ ۴۰ AD + ۲ local + ۱ proof (۲۰+۲۰+۱۰) = ۷۰  
✅ ۲۰ AD + ۳ local + ۲ proof (۳۰+۲۰+۲۰) = ۹۰ → قبوله!
✅ ۱۰ AD + ۳ standalone کامل (۶۰) = ۷۰
```

---

## ⏰ جدول زمانی ۲۴ ساعت + ۲۴ ساعت

| زمان | وظیفه |
|------|-------|
| **۰۰:۰۰** | شروع آزمون، اتصال VPN، ورود به Proctoring |
| **۰۰:۰۰-۰۱:۰۰** | اسکن کامل کل شبکه (`nmap -sn` سپس `-sC -sV -p-`) |
| **۰۱:۰۰-۰۸:۰۰** | **حمله به Standalone Machines** (سریع‌ترین امتیاز) |
| **۰۸:۰۰-۱۸:۰۰** | **حمله به AD Set** (۴۰ امتیاز حیاتی) |
| **۱۸:۰۰-۲۳:۰۰** | جمع‌بندی، گرفتن اسکرین‌شات، چک کردن flags |
| **۲۳:۰۰-۲۳:۴۵** | submit flags در Control Panel |
| **۲۳:۴۵** | پایان آزمون |
| **۲۴ ساعت بعد** | آپلود گزارش PDF در `upload.offsec.com` |

---

## 🛠️ محدودیت‌های ابزار (ممنوعات = دیسکوالنی)

| ❌ ممنوع کاملاً | ❌ ممنوع محدود | ✅ مجاز |
|---------------|--------------|---------|
| Spoofing (IP, ARP, DNS, NBNS) | **Metasploit** فقط روی **یک** ماشین | Nmap + NSE |
| Commercial tools (Metasploit Pro, Burp Pro) | **Meterpreter** فقط روی **یک** ماشین | Nikto, Gobuster |
| Automatic exploitation (SQLmap, SQLninja, db_autopwn) | `check` در msfconsole = استفاده محسوب میشه | Burp Suite Free |
| Mass scanners (Nessus, OpenVAS, Nexpose) | Metasploit برای pivoting = ممنوع | DirBuster |
| AI Chatbots (ChatGPT, Gemini, DeepSeek, KAI) | | msfvenom (مجاز برای همه) |
| | | exploit/multi/handler (مجاز) |

> ⚠️ **قانون طلایی Metasploit:** به محض اینکه یه ماژول (Auxiliary/Exploit/Post) یا Meterpreter رو روی یه ماشین زدی، قفل شدی! اگر fail هم بشه، دیگه نمی‌تونی روی ماشین دیگه استفاده کنی.

---

## 📝 چک‌لیست گزارش‌نویسی (بدون اینا صفر می‌شی!)

### برای هر ماشین باید داشته باشی:

```
□ Screenshot از local.txt با:
   └── type local.txt یا cat local.txt
   └── و در همان اسکرین: ipconfig / ifconfig / ip addr

□ Screenshot از proof.txt با:
   └── type proof.txt یا cat proof.txt  
   └── و در همان اسکرین: ipconfig / ifconfig / ip addr

□ توضیح مرحله به مرحله exploitation
□ دستورات کامل با خروجی (copy-paste از ترمینال)
□ اگر exploit تغییر دادی:
   ├── کد تغییر یافته
   ├── URL exploit اصلی
   ├── تغییرات هایلایت شده
   └── توضیح چرا تغییر دادی
```

### فرمت ارسال:

```bash
# نام‌گذاری دقیق (case-sensitive!)
OSCP-OS-XXXXX-Exam-Report.pdf
OSCP-OS-XXXXX-Exam-Report.7z

# ساخت archive (بدون پسورد!)
sudo 7z a OSCP-OS-XXXXX-Exam-Report.7z OSCP-OS-XXXXX-Exam-Report.pdf

# چک سایز (زیر ۲۰۰MB)
ls -lh OSCP-OS-XXXXX-Exam-Report.7z

# آپلود و چک MD5
sudo md5sum OSCP-OS-XXXXX-Exam-Report.7z
# مقایسه با MD5 سایت upload.offsec.com
```

> 🔴 **مهم:** فقط PDF داخل ۷z باشه. هیچ فایل دیگه‌ای! اسکریپت‌ها رو داخل PDF بذار.

---

## 🎥 نکات Proctoring (نظارت)

| نکته | توضیح |
|------|-------|
| **مرورگر** | Chrome, Firefox, Brave, Edge, Chromium |
| **Linux + چند مانیتور** | حتماً **Chrome** استفاده کن (نه Firefox) |
| **Wayland ممنوع** | باید **Xorg/X11** باشه. قبل آزمون چک کن: `echo $XDG_SESSION_TYPE` |
| **وب‌کم** | باید روشن باشه و صورتت معلوم باشه |
| **Screen Share** | **Your Entire Screen** رو انتخاب کن. هیچوقت Stop Sharing نزن! |
| **استراحت** | از دکمه **PAUSE WEBCAM** استفاده کن، بعدش RESUME کن |
| **۱۵ دقیقه قبل** | باید login کنی توی سیستم پروکتورینگ |
| **VPN issues** | توی چت پروکتورینگ بگو، نه ایمیل! |

```bash
echo $XDG_SESSION_TYPE
```
```bash
# فایل کانفیگ رو ادیت کن
sudo nano /etc/gdm3/custom.conf
# یا
sudo nano /etc/gdm/custom.conf

# این خط رو پیدا کن:
#WaylandEnable=false
# # رو بردار تا بشه:
WaylandEnable=false

# سیو کن و reboot
sudo reboot
```

---

## 🔥 دستورات آماده روز آزمون

### ۱. شروع و اتصال

```bash
# اکسترکت و اتصال VPN
tar xvfj exam-connection.tar.bz2
sudo openvpn OS-XXXXXX-OSCP.ovpn
# یوزر/پسورد رو از ایمیل وارد کن

# چک اتصال
ip addr show tun0
ping <gateway-exam>
```

### ۲. Enumeration اولیه (ساعت ۰)

```bash
# پیدا کردن هاست‌های زنده
sudo nmap -sn <subnet>/24 -oN hosts-alive.txt

# اسکن کامل سریع
sudo nmap -sC -sV --open -oN fast-scan.txt -iL hosts-alive.txt

# اسکن همه پورت‌ها (بذر پس‌زمینه اجرا شه)
sudo nmap -sC -sV -p- --open -oN full-scan.txt -iL hosts-alive.txt &
```

### ۳. AD Set - شروع سریع

```powershell
# با creds داده شده وصل شو (RDP یا SSH)
xfreerdp /u:Eric.Wallows /p:EricLikesRunning800 /v:<AD-IP>

# یا از Kali:
crackmapexec smb <AD-IP> -u Eric.Wallows -p EricLikesRunning800

# Enumeration سریع AD
Import-Module .\PowerView.ps1
Get-NetDomain
Get-NetUser | select cn
Get-NetGroup | select cn
Get-NetComputer | select dnshostname,operatingsystem
Find-LocalAdminAccess
```

### ۴. اسکرین‌شات استاندارد

```bash
# Linux target
cat /root/proof.txt && ip addr

# Windows target  
type C:\Users\Administrator\Desktop\proof.txt && ipconfig
```

---

## ⚡ استراتژی طلایی روز آزمون

| اگر این اتفاق افتاد | این کار رو بکن |
|-------------------|---------------|
| ۲ ساعت روی یه ماشین گیر کردی | برو ماشین بعدی! برگرد بعداً |
| Exploit کار نکرد | ورژن رو چک کن، پارامترها رو بخون، شاید نیاز به تغییر کوچک داره |
| Metasploit خواستی بزنی | فقط روی **سخت‌ترین standalone** که راه دیگه نداره! |
| Reverse shell گرفتی | سریع `python3 -c 'import pty; pty.spawn("/bin/bash")'` یا TTY ارتقا بده |
| Administrative shell گرفتی | سریع proof.txt بگیر و اسکرین‌شات کن |
| VPN قطع شد | توی چت پروکتورینگ بگو. ایمیل نده! |
| ماشین خراب شد | Revert کن (۲۴ تا فرصت داری، یکبار هم ریست میشه) |

---

## 🎯 خلاصه فراموش‌نشدنی

```
۱. شروع با AD Enumeration (چون creds رو داری)
۲. سریع‌ترین امتیازها رو بگیر (local.txt ها)
۳. هر shell = اسکرین‌شات + proof
۴. Metasploit = آخرین راه‌حل و فقط یک ماشین
۵. گزارش = PDF → ۷z → upload.offsec.com → MD5 match
۶. X11 باید باشه، Wayland نه
۷. Stop Sharing نزن!
۸. ۷۰ امتیاز = Pass (دیگه bonus نیست)
```

کدوم بخش رو عمیق‌تر توضیح بدم؟ مثلاً template گزارش یا اسکریپت‌های آماده؟