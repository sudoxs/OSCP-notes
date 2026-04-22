
|Nmap Finding / Example|What It Means|Target Type|What You Should Think|Recommended Attack Path|
|---|---|---|---|---|
|`3389/tcp open ms-wbt-server`|RDP is enabled|User workstation|Human user logs in here|Client-Side Attack|
|**Example:** `3389/tcp open ms-wbt-server Microsoft Terminal Services`||||Windows Library / Shortcut|
|`NetBIOS_Computer_Name: CLIENT137`|Workstation naming|Client system|Real user interaction|Social Engineering|
|**Example:** `Target_Name: CLIENT137`||||Library-ms + .lnk|
|`139/tcp open netbios-ssn`|SMB legacy|File sharing enabled|File delivery possible|SMB file drop|
|`445/tcp open microsoft-ds`|SMB modern|Windows file shares|Payload delivery|smbclient|
|**Example:** `445/tcp open microsoft-ds?`|||||
|`smb2-security-mode: signing enabled but not required`|SMB relay possible|Weak SMB security|Easier abuse|SMB attacks|
|`80/tcp open http Microsoft-IIS`|Web server|Service machine|No user click|Web exploitation|
|**Example:** `http-server-header: Microsoft-IIS/10.0`|||||
|`5985/tcp open http Microsoft-HTTPAPI`|WinRM enabled|Admin / Server|Credential-based|Evil-WinRM|
|`25/tcp open smtp`|Mail server|Email infra|Phishing possible|Client delivery|
|**Example:** `smtp-commands: AUTH LOGIN`|||||
|`110/tcp open pop3`|Mailbox access|Users exist|Human target|Credential reuse|
|`143/tcp open imap`|Mail access|Corporate users|Social attack|Email vectors|
|`OS: Windows 10 / 11`|Modern OS|Hardened|Fewer exploits|User-based|
|`OS: Windows Server 2016+`|Server OS|Infra|Service exploitation|PrivEsc|
|`Product_Version: 10.0.22000`|Windows 11|User desktop|Macro restricted|Library attack|
|`RDP + SMB + CLIENT name`|Combined signal|Perfect bait|Click required|Library + Shortcut|




                ┌───────────────┐
                │  NMAP RESULT  │
                └───────┬───────┘
                        │
        ┌───────────────┴───────────────┐
        │                               │
	  Is RDP (3389) open?             No RDP
        │                               │
      YES                              Exploit / Service Attack
        │
  ┌─────┴────────┐
  │                                              │
CLIENT / HR /               SERVER / DC
USER MACHINE
  │
YES (CLIENT)
  │
Is SMB (445) open?
 │
┌─┴───────────────┐
│                                                        │
YES                                                  NO
│                                                        │
Library / LNK                       Macro / HTA



|دیدی|تصمیم|
|---|---|
|3389 + CLIENT + 445|**Library + LNK**|
|3389 + CLIENT + NO SMB|**HTA یا Macro**|
|SMTP / POP3 / IMAP|**Macro یا Library**|
|IIS / SQL / WinRM|**Exploit**|
|DC / SERVER|**No client-side**|

---

## 1️⃣ پورت 3389 (RDP)

### اگر دیدی:

```
3389/tcp open ms-wbt-server
```

### معنی‌اش:

- ویندوز **کاربری**
    
- لاگین انسانی
    
- File Explorer
    
- کلیک، دوبل‌کلیک
    

### تصمیم:

✅ **Client-side attack**

- Windows Library (.Library-ms)
    
- Shortcut (.lnk)
    
- Macro / HTA (بسته به شرایط)
    

📌 این مهم‌ترین نشانه است.

---

## 2️⃣ نام ماشین (خیلی مهم)

### اینو ببین:

```
NetBIOS_Computer_Name: CLIENT137
Target_Name: CLIENT137
```

یا:

```
Host: ADMIN
```

### تفسیر:

|نام|معنی|
|---|---|
|CLIENT|سیستم کاربر|
|HR / FINANCE|سیستم کاربر|
|DC / SERVER|سرور|
|ADMIN|معمولاً سرور|

📌 CLIENT + RDP = ۹۰٪ فریب کاربر

---

## 3️⃣ SMB باز (445 + 139)

```
139/tcp open netbios-ssn
445/tcp open microsoft-ds
```

### معنی:

- فایل شیر
    
- ایمیل شبیه‌سازی‌شده
    
- تحویل payload
    

📌 SMB کنار RDP یعنی:

> «می‌تونی فایل بدی دست کاربر»

---

## 4️⃣ IIS / SQL / WinRM (نشانه سرور)

اگر دیدی:

```
80/tcp open http Microsoft-IIS
1433/tcp open mssql
5985/tcp open winrm
```

### معنی:

- ماشین **سرویس‌محور**
    
- Exploit
    
- Credential abuse
    
- No user click
    

📌 اینا معمولاً **Library نمی‌خوان**

---

## 5️⃣ SMTP / POP3 / IMAP (نشانه اجتماعی)

مثل این:

```
25/tcp open smtp
110/tcp open pop3
143/tcp open imap
```

### معنی:

- ایمیل
    
- کاربر
    
- Phishing
    

📌 این خیلی قوی نشونه‌ی client-side attackـه  
(مخصوصاً Library + Shortcut)

---

## 🧩 حالا خروجی خودت رو تفسیر کنیم (سریع)

### ماشین 192.168.125.194

- ✔️ 3389 RDP
    
- ✔️ SMB
    
- ✔️ اسم CLIENT137
    

✅ نتیجه:

```
Client machine
→ user interaction
→ Windows Library / Shortcut
```

---

### ماشین 192.168.125.199

- SMTP / IMAP / POP3
    
- SMB
    
- IIS
    
- اسم ADMIN
    

📌 این یکی معمولاً:

- infrastructure
    
- یا pivot
    
- یا delivery point
    

نه هدف نهایی کلیک

---

## 🧠 چک‌لیست ۵ ثانیه‌ای سر جلسه

به خروجی nmap نگاه کن و بپرس:

1. ❓ 3389 بازه؟ → بله = انسان
    
2. ❓ اسم CLIENTـه؟ → بله = فریب
    
3. ❓ SMB بازه؟ → بله = تحویل فایل
    
4. ❓ IIS/SQL داره؟ → بله = exploit
    

اگر 1+2+3 = YES  
🎯 **Library attack رو امتحان کن**

---

## ✨ خلاصه نهایی (طلایی)

```
RDP + SMB + CLIENT name
= Windows Library / Shortcut attack
```

---

اگر بخوای، قدم بعدی می‌تونم:

- همین رو تبدیل کنم به **decision tree**
    
- یا بگم **کی Library، کی Macro، کی HTA**
    
- یا دقیقاً بگم **تو Capstone Lab از کجا شروع کنی**
    

فقط بگو 🔥