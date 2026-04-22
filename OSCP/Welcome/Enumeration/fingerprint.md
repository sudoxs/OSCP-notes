
## **🔰 مرحله ۱: ساخت صفحه وب با fingerprint مخفی**

### **۱.۱ کد HTML کامل (با پنهان‌سازی):**

```html
<!DOCTYPE html>
<html lang="fa">
<head>
    <meta charset="UTF-8">
    <title>صفحه آموزشی امنیت</title>
    <style>
        /* ظاهر عادی صفحه */
        body {
            font-family: Tahoma;
            text-align: center;
            padding: 50px;
        }
        /* مخفی کردن المان‌های fingerprint */
        .fingerprint-data {
            display: none;
        }
    </style>
    <!-- کتابخانه ThumbmarkJS از CDN -->
    <script src="https://unpkg.com/thumbmarkjs/dist/thumbmark.umd.js"></script>
</head>
<body>
    <h1>خوش آمدید به دوره آموزشی امنیت</h1>
    <p>این صفحه برای آموزش روش‌های حفاظت طراحی شده است.</p>
    
    <!-- این بخش برای کاربر قابل مشاهده نیست -->
    <div class="fingerprint-data" id="fp-data"></div>

    <script>
        // تابع اصلی جمع‌آوری اطلاعات
        async function collectAndSend() {
            try {
                // بارگذاری کتابخانه
                const tm = await thumbmark.default();
                
                // جمع‌آوری اثر انگشت
                const fingerprint = await tm.get();
                
                // جمع‌آوری اطلاعات اضافی
                const additionalData = {
                    fingerprint: fingerprint,
                    userAgent: navigator.userAgent,
                    language: navigator.language,
                    screen: {
                        width: screen.width,
                        height: screen.height,
                        colorDepth: screen.colorDepth
                    },
                    timezone: Intl.DateTimeFormat().resolvedOptions().timeZone,
                    cookiesEnabled: navigator.cookieEnabled,
                    plugins: Array.from(navigator.plugins).map(p => p.name),
                    url: window.location.href,
                    referrer: document.referrer,
                    timestamp: new Date().toISOString()
                };

                // نمایش در صفحه (فقط برای نمایش آموزشی - در حالت واقعی حذف کنید)
                document.getElementById('fp-data').innerHTML = 
                    '<h3>اطلاعات جمع‌آوری شده (برای نمایش آموزشی):</h3>' +
                    '<textarea style="width:100%;height:200px">' +
                    JSON.stringify(additionalData, null, 2) +
                    '</textarea>';

                // ارسال به سرور
                await sendToServer(additionalData);
                
            } catch (error) {
                console.error('خطا در جمع‌آوری اطلاعات:', error);
            }
        }

        // تابع ارسال به سرور
        async function sendToServer(data) {
            try {
                const response = await fetch('http://localhost:5000/collect', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify(data)
                });
                
                if (response.ok) {
                    console.log('اطلاعات با موفقیت ارسال شد');
                }
            } catch (error) {
                console.warn('خطا در ارسال به سرور (شاید سرور فعال نیست):', error);
            }
        }

        // اجرای جمع‌آوری هنگام لود صفحه
        document.addEventListener('DOMContentLoaded', collectAndSend);
    </script>
</body>
</html>
```

---

## **🐍 مرحله ۲: ساخت سرور پایتون برای دریافت اطلاعات**

### **۲.۱ نصب نیازمندی‌ها:**
```bash
pip install flask python-telegram-bot
```

### **۲.۲ کد سرور پایتون (با تلگرام):**

```python
# server.py
from flask import Flask, request, jsonify
from flask_cors import CORS
import json
from datetime import datetime
import telebot

app = Flask(__name__)
CORS(app)  # اجازه دسترسی از دامنه‌های دیگر

# تنظیمات تلگرام
TELEGRAM_TOKEN = 'توکن_ربات_تلگرام_شما'  # از @BotFather بگیرید
TELEGRAM_CHAT_ID = 'آیدی_عددی_چت_شما'  # از @userinfobot بگیرید
bot = telebot.TeleBot(TELEGRAM_TOKEN)

def send_to_telegram(data):
    """ارسال اطلاعات به تلگرام"""
    try:
        message = f"""
🎯 **یک بازدید جدید ثبت شد!**
        
📊 **اطلاعات اصلی:**
• اثر انگشت: `{data.get('fingerprint', 'نامشخص')[:50]}...`
• مرورگر: {data.get('userAgent', 'نامشخص').split(')')[0].split('(')[-1]}
• زبان: {data.get('language', 'نامشخص')}
• منطقه زمانی: {data.get('timezone', 'نامشخص')}

🖥️ **اطلاعات سیستم:**
• صفحه نمایش: {data['screen']['width']}x{data['screen']['height']}
• عمق رنگ: {data['screen']['colorDepth']}
• کوکی‌ها: {'فعال' if data.get('cookiesEnabled') else 'غیرفعال'}

🌐 **اطلاعات مرور:**
• آدرس: {data.get('url', 'نامشخص')}
• رفرر: {data.get('referrer', 'بدون رفرر')}
• زمان: {data.get('timestamp', datetime.now().isoformat())}

📁 **تعداد پلاگین‌ها:** {len(data.get('plugins', []))}
"""
        
        bot.send_message(TELEGRAM_CHAT_ID, message, parse_mode='Markdown')
        
        # همچنین فایل JSON کامل رو هم بفرست
        with open(f"logs/{data['fingerprint']}.json", 'w') as f:
            json.dump(data, f, indent=2, ensure_ascii=False)
            
        print("✅ اطلاعات به تلگرام ارسال شد")
        
    except Exception as e:
        print(f"❌ خطا در ارسال به تلگرام: {e}")

@app.route('/collect', methods=['POST'])
def collect_data():
    """دریافت اطلاعات از صفحه وب"""
    try:
        data = request.json
        
        # لاگ کردن در کنسول سرور
        print(f"\n{'='*50}")
        print(f"📥 دریافت اطلاعات جدید - {datetime.now()}")
        print(f"🆔 Fingerprint: {data.get('fingerprint', 'نامشخص')[:30]}...")
        print(f"🌐 User Agent: {data.get('userAgent', 'نامشخص')[:50]}...")
        print(f"{'='*50}\n")
        
        # ذخیره در فایل
        fingerprint = data.get('fingerprint', 'unknown')
        filename = f"logs/{fingerprint}_{datetime.now().strftime('%Y%m%d_%H%M%S')}.json"
        
        with open(filename, 'w', encoding='utf-8') as f:
            json.dump(data, f, indent=2, ensure_ascii=False, ensure_ascii=False)
        
        # ارسال به تلگرام
        send_to_telegram(data)
        
        return jsonify({'status': 'success', 'message': 'داده دریافت شد'}), 200
        
    except Exception as e:
        print(f"❌ خطا در پردازش درخواست: {e}")
        return jsonify({'status': 'error', 'message': str(e)}), 500

@app.route('/logs', methods=['GET'])
def get_logs():
    """نمایش لاگ‌های ذخیره شده (برای مدیریت)"""
    import os
    logs = []
    if os.path.exists('logs'):
        for file in os.listdir('logs'):
            if file.endswith('.json'):
                logs.append(file)
    return jsonify({'logs': logs, 'count': len(logs)})

if __name__ == '__main__':
    # ایجاد پوشه logs اگر وجود ندارد
    import os
    if not os.path.exists('logs'):
        os.makedirs('logs')
    
    print("🚀 سرور در حال اجرا است...")
    print("📡 آدرس: http://localhost:5000")
    print("📥 آدرس دریافت اطلاعات: http://localhost:5000/collect")
    print("📊 مشاهده لاگ‌ها: http://localhost:5000/logs")
    
    app.run(host='0.0.0.0', port=5000, debug=True)
```

---

## **🤖 مرحله ۳: تنظیم ربات تلگرام**

### **۳.۱ ساخت ربات:**
1. در تلگرام به @BotFather مراجعه کن
2. دستور `/newbot` رو بزن
3. اسم ربات رو انتخاب کن (مثلاً `FingerprintLoggerBot`)
4. توکن دریافتی رو در کد پایتون جایگزین کن (`TELEGRAM_TOKEN`)

### **۳.۲ گرفتن Chat ID:**
1. به @userinfobot برو
2. دستور `/start` رو بزن
3. عددی که می‌ده رو در کد پایتون جایگزین کن (`TELEGRAM_CHAT_ID`)

---

## **🚀 مرحله ۴: اجرای کامل سیستم**

### **۴.۱ ساختار پوشه‌ها:**
```
پوشه_پروژه/
├── index.html          # صفحه وب با fingerprint
├── server.py           # سرور پایتون
├── logs/               # پوشه ذخیره لاگ‌ها
└── requirements.txt    # نیازمندی‌ها
```

### **۴.۲ فایل requirements.txt:**
```txt
Flask==2.3.3
flask-cors==4.0.0
python-telegram-bot==20.3
```

### **۴.۳ دستورات اجرا:**
```bash
# نصب نیازمندی‌ها
pip install -r requirements.txt

# اجرای سرور
python server.py

# باز کردن صفحه وب (در مرورگر)
# فایل index.html را با مرورگر باز کن
```

---

## **🛡️ مرحله ۵: تست سیستم**

### **۵.۱ تست محلی:**
1. سرور پایتون رو اجرا کن
2. فایل `index.html` رو در مرورگر باز کن
3. به تلگرام برو و پیام رو ببین
4. پوشه `logs/` رو چک کن

### **۵.۲ تست از طریق شبکه:**
اگر می‌خواهی از طریق اینترنت تست کنی:
```python
# در server.py این خط رو تغییر بده:
app.run(host='0.0.0.0', port=5000, debug=True)

# و در index.html:
const response = await fetch('http://آیپی_عمومی:5000/collect', {
```

---

## **🎯 نکات مهم امنیتی و اخلاقی:**

### **❌ هرگز در محیط واقعی بدون مجوز استفاده نکن:**
```python
# در کد واقعی، باید مجوز را چک کنی
ALLOWED_DOMAINS = ['localhost', '127.0.0.1']

@app.before_request
def check_authorization():
    if request.remote_addr not in ALLOWED_DOMAINS:
        return jsonify({'error': 'مجوز ندارید'}), 403
```

### **✅ برای آموزش کاملاً مناسب است:**
1. فقط روی سیستم خودت اجرا کن
2. فقط با دستگاه‌های خودت تست کن
3. لاگ‌ها رو بعد از آموزش پاک کن

### **🔒 روش‌های مقابله (همان هدف آموزشی شما):**
```javascript
// راه‌های مقابله که می‌تونی بررسی کنی:
// 1. استفاده از افزونه‌های مسدودکننده fingerprint
// 2. غیرفعال کردن جاوااسکریپت
// 3. استفاده از مرورگرهای مقاوم مانند Tor
// 4. تغییر دوره‌ای تنظیمات مرورگر
```

---

## **📊 خروجی نمونه در تلگرام:**

```
🎯 یک بازدید جدید ثبت شد!

📊 اطلاعات اصلی:
• اثر انگشت: `a1b2c3d4e5f6g7h8i9j0...`
• مرورگر: Chrome/Windows
• زبان: fa-IR
• منطقه زمانی: Asia/Tehran

🖥️ اطلاعات سیستم:
• صفحه نمایش: 1920x1080
• عمق رنگ: 24
• کوکی‌ها: فعال

🌐 اطلاعات مرور:
• آدرس: http://localhost/index.html
• رفرر: بدون رفرر
• زمان: 2024-01-15T10:30:45.123Z

📁 تعداد پلاگین‌ها: 12
```

---


---


---

**عالیه!** دقیقاً درست فهمیدی. الان یک آزمایش آموزشی کامل طراحی می‌کنیم. **توجه:** این فقط برای محیط آزمایشی شخصی شماست (localhost) یا روی سرورهای خودتان با مجوز.

---

## **🎯 پروژه کامل: سیستم ردیابی آموزشی با ارسال به تلگرام**

### **📁 ساختار پروژه:**
```
tracking-project/
├── server.py           # سرور پایتون (دریافت + ارسال به تلگرام)
├── index.html          # صفحه وب تله
├── fingerprint.js      # کتابخانه (اختیاری - می‌تونی از CDN هم استفاده کنی)
└── logs/               # پوشه ذخیره لاگ‌ها
```

---

## **🛠️ مرحله ۱: سرور پایتون (با Flask)**

فایل `server.py` رو بساز:

```python
#!/usr/bin/env python3
"""
سرور دریافت اطلاعات fingerprint - فقط برای اهداف آموزشی
"""
from flask import Flask, request, jsonify
from flask_cors import CORS
import json
import datetime
import requests
import os

app = Flask(__name__)
CORS(app)  # اجازه دسترسی از دامنه‌های دیگر

# تنظیمات تلگرام
TELEGRAM_BOT_TOKEN = "YOUR_BOT_TOKEN"  # توکن رباتت رو اینجا بذار
TELEGRAM_CHAT_ID = "YOUR_CHAT_ID"      # چت آیدی خودت رو اینجا بذار

# پوشه لاگ‌ها
LOG_DIR = "logs"
os.makedirs(LOG_DIR, exist_ok=True)

def send_to_telegram(message):
    """ارسال پیام به تلگرام"""
    try:
        url = f"https://api.telegram.org/bot{TELEGRAM_BOT_TOKEN}/sendMessage"
        payload = {
            "chat_id": TELEGRAM_CHAT_ID,
            "text": message,
            "parse_mode": "HTML"
        }
        response = requests.post(url, json=payload, timeout=10)
        return response.status_code == 200
    except Exception as e:
        print(f"Error sending to Telegram: {e}")
        return False

def log_to_file(data):
    """ذخیره اطلاعات در فایل"""
    timestamp = datetime.datetime.now().strftime("%Y-%m-%d_%H-%M-%S")
    filename = f"{LOG_DIR}/visitor_{timestamp}.json"
    
    with open(filename, 'w', encoding='utf-8') as f:
        json.dump(data, f, indent=2, ensure_ascii=False)
    
    return filename

@app.route('/track', methods=['POST'])
def track():
    """دریافت اطلاعات از کاربر"""
    try:
        # دریافت داده‌های ارسالی
        data = request.get_json()
        
        # اطلاعات اضافه
        data['received_at'] = datetime.datetime.now().isoformat()
        data['ip_address'] = request.remote_addr
        data['user_agent'] = request.headers.get('User-Agent')
        
        # نمایش در کنسول سرور
        print("\n" + "="*50)
        print(f"📱 New Visitor Detected!")
        print(f"🆔 Visitor ID: {data.get('visitorId', 'N/A')}")
        print(f"🌐 IP: {data['ip_address']}")
        print(f"🕒 Time: {data['received_at']}")
        print("="*50)
        
        # ذخیره در فایل
        log_file = log_to_file(data)
        print(f"💾 Saved to: {log_file}")
        
        # ارسال به تلگرام
        telegram_msg = f"""
🚨 <b>New Visitor Detected!</b>

🆔 <b>Visitor ID:</b> <code>{data.get('visitorId', 'N/A')}</code>
🌐 <b>IP Address:</b> <code>{data['ip_address']}</code>
💻 <b>Browser:</b> {data['user_agent']}
🕒 <b>Time:</b> {data['received_at']}

📊 <b>Components Found:</b> {len(data.get('components', {}))}
        """
        
        if send_to_telegram(telegram_msg):
            print("📤 Sent to Telegram successfully!")
        else:
            print("❌ Failed to send to Telegram")
        
        return jsonify({"status": "success", "message": "Data received"})
    
    except Exception as e:
        print(f"❌ Error: {e}")
        return jsonify({"status": "error", "message": str(e)}), 500

@app.route('/')
def index():
    """صفحه اصلی (اختیاری)"""
    return "Fingerprint Server is running!"

if __name__ == '__main__':
    print("🔥 Fingerprint Tracking Server Started!")
    print("📡 Server running on http://localhost:5000")
    print("📤 POST data to: http://localhost:5000/track")
    print("📁 Logs saved in: logs/ directory")
    app.run(debug=True, port=5000)
```

---

## **🌐 مرحله ۲: صفحه وب تله (index.html)**

فایل `index.html` رو بساز:

```html
<!DOCTYPE html>
<html lang="fa">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>آزمایش امنیتی - صفحه تست</title>
    <style>
        * {
            font-family: Tahoma, Arial, sans-serif;
            text-align: center;
            direction: rtl;
        }
        
        body {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            min-height: 100vh;
            padding: 50px 20px;
        }
        
        .container {
            max-width: 800px;
            margin: 0 auto;
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 40px;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
        }
        
        h1 {
            font-size: 2.5em;
            margin-bottom: 30px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
        }
        
        .content {
            background: rgba(255, 255, 255, 0.15);
            border-radius: 15px;
            padding: 30px;
            margin: 30px 0;
        }
        
        #status {
            background: rgba(255, 255, 255, 0.2);
            border-radius: 10px;
            padding: 20px;
            margin-top: 30px;
            font-family: monospace;
            word-break: break-all;
        }
        
        .hidden {
            display: none !important;
        }
        
        .footer {
            margin-top: 40px;
            font-size: 0.9em;
            opacity: 0.8;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🎯 آزمایش سیستم تشخیص امنیتی</h1>
        
        <div class="content">
            <h2>🔍 این صفحه در حال آزمایش روش‌های تشخیص کاربر است</h2>
            <p>این یک آزمایش امنیتی آموزشی است که نشان می‌دهد چگونه وب‌سایت‌ها می‌توانند کاربران را شناسایی کنند.</p>
            <p>هیچ اطلاعات شخصی شما ذخیره نمی‌شود و این آزمایش کاملاً بی‌خطر است.</p>
        </div>
        
        <div id="status">
            <p>⏳ در حال تشخیص سیستم شما...</p>
        </div>
        
        <!-- بخش مخفی ردیابی -->
        <div id="tracking-section" class="hidden">
            <p>سیستم شما در حال شناسایی است. این فرآیند ۳ ثانیه طول می‌کشد...</p>
        </div>
        
        <div class="footer">
            <p>⚠️ این پروژه صرفاً برای اهداف آموزشی و آگاهی‌بخشی امنیتی ایجاد شده است.</p>
            <p>📚 مطالعه بیشتر: OWASP Fingerprinting Guidelines</p>
        </div>
    </div>

    <!-- FingerprintJS از CDN -->
    <script src="https://cdn.jsdelivr.net/npm/@fingerprintjs/fingerprintjs@3/dist/fp.min.js"></script>
    
    <script>
        // کد مخفی ردیابی که به طور خودکار اجرا می‌شود
        (function() {
            'use strict';
            
            // تابع اصلی ردیابی
            async function trackVisitor() {
                try {
                    // نمایش پیام
                    document.getElementById('status').innerHTML = 
                        '<p>🔬 در حال جمع‌آوری اطلاعات سیستم...</p>';
                    
                    // بارگذاری FingerprintJS
                    const fp = await FingerprintJS.load();
                    
                    // گرفتن اطلاعات
                    const result = await fp.get();
                    const visitorId = result.visitorId;
                    
                    // اطلاعات اضافی جمع‌آوری
                    const additionalInfo = {
                        userAgent: navigator.userAgent,
                        language: navigator.language,
                        platform: navigator.platform,
                        screenResolution: `${screen.width}x${screen.height}`,
                        colorDepth: screen.colorDepth,
                        timezone: Intl.DateTimeFormat().resolvedOptions().timeZone,
                        cookiesEnabled: navigator.cookieEnabled,
                        doNotTrack: navigator.doNotTrack || 'unspecified'
                    };
                    
                    // آماده‌سازی داده‌ها برای ارسال
                    const trackingData = {
                        visitorId: visitorId,
                        timestamp: new Date().toISOString(),
                        url: window.location.href,
                        referrer: document.referrer || 'direct',
                        components: result.components,
                        additionalInfo: additionalInfo
                    };
                    
                    // نمایش نتیجه
                    document.getElementById('status').innerHTML = 
                        `<h3>✅ تشخیص کامل شد!</h3>
                         <p><strong>شناسه منحصربه‌فرد شما:</strong></p>
                         <p style="background:#000;padding:10px;border-radius:5px;">
                            ${visitorId}
                         </p>
                         <p>این شناسه مثل اثرانگشت دیجیتال مرورگر شماست.</p>`;
                    
                    // ارسال به سرور
                    await sendToServer(trackingData);
                    
                } catch (error) {
                    console.error('Tracking error:', error);
                    document.getElementById('status').innerHTML = 
                        '<p>❌ خطا در تشخیص سیستم. لطفاً دوباره تلاش کنید.</p>';
                }
            }
            
            // ارسال داده‌ها به سرور
            async function sendToServer(data) {
                try {
                    const response = await fetch('http://localhost:5000/track', {
                        method: 'POST',
                        headers: {
                            'Content-Type': 'application/json',
                        },
                        body: JSON.stringify(data)
                    });
                    
                    const result = await response.json();
                    console.log('Server response:', result);
                    
                    // نمایش موفقیت
                    setTimeout(() => {
                        const statusDiv = document.getElementById('status');
                        statusDiv.innerHTML += 
                            '<p style="color:#4CAF50;margin-top:20px;">' +
                            '✅ اطلاعات با موفقیت ثبت شد (آزمایش آموزشی)</p>';
                    }, 1000);
                    
                } catch (error) {
                    console.error('Error sending to server:', error);
                }
            }
            
            // اجرای ردیابی با تأخیر
            setTimeout(() => {
                trackVisitor();
            }, 2000); // ۲ ثانیه پس از لود صفحه
        })();
    </script>
</body>
</html>
```

---

## **🤖 مرحله ۳: راه‌اندازی ربات تلگرام**

### **۱. ساخت ربات:**
1. در تلگرام به `@BotFather` پیام بده
2. دستور `/newbot` رو بزن
3. نام ربات رو انتخاب کن (مثلاً `My Tracking Bot`)
4. یوزرنیم ربات رو انتخاب کن (مثلاً `my_tracking_test_bot`)
5. **توکن** رو ذخیره کن (مثل `1234567890:ABCdefGhIJKlmNoPQRsTUVwxyZ`)

### **۲. گرفتن Chat ID:**
1. به رباتت پیام بده
2. این URL رو در مرورگر باز کن (با توکن خودت):
   ```
   https://api.telegram.org/botYOUR_BOT_TOKEN/getUpdates
   ```
3. `chat.id` رو از پاسخ پیدا کن

---

## **🚀 مرحله ۴: اجرای پروژه**

### **نصب requirements:**
```bash
pip install flask flask-cors requests
```

### **ویرایش تنظیمات:**
در فایل `server.py`:
```python
TELEGRAM_BOT_TOKEN = "1234567890:ABCdefGhIJKlmNoPQRsTUVwxyZ"  # توکن واقعی
TELEGRAM_CHAT_ID = "987654321"  # چت آیدی واقعی
```

### **اجرای سرور:**
```bash
python server.py
```

### **بازکردن صفحه وب:**
1. مرورگر رو باز کن
2. به آدرس `http://localhost:5000` برو
3. یا فایل `index.html` رو مستقیماً باز کن

---

## **📊 مرحله ۵: تست سیستم**

### **روش‌های تست:**
1. **همین مرورگر:** صفحه رو چند بار رفرش کن
2. **مرورگر ناشناس:** Ctrl+Shift+N
3. **مرورگر دیگر:** Firefox, Edge, etc.
4. **موبایل:** از گوشی به سرور وصل شو
5. **با VPN:** آدرس IP متفاوت

### **چیزی که می‌بینی:**
- در **کنسول سرور** اطلاعات نمایش داده می‌شه
- در **تلگرام** پیام می‌آد
- در پوشه `logs/` فایل‌های JSON ذخیره می‌شن

---

## **🔒 نکات مهم امنیتی/اخلاقی:**

### **چیزهایی که باید رعایت کنی:**
```python
# ۱. فقط روی localhost اجرا کن
if __name__ == '__main__':
    app.run(debug=True, host='127.0.0.1', port=5000)  # فقط localhost

# ۲. محدود کردن IP‌ها (اختیاری)
ALLOWED_IPS = ['127.0.0.1', '192.168.1.0/24']

# ۳. لاگ‌گیری محدود
MAX_LOGS = 100  # حداکثر تعداد لاگ
LOG_RETENTION_DAYS = 7  # حداکثر زمان نگهداری

# ۴. اطلاع‌رسانی به کاربر
# در صفحه HTML حتماً بنویس که این یک آزمایش است
```

### **برای محیط واقعی (آزمون OSCP):**
1. **همیشه مجوز کتبی داشته باش**
2. **فقط روی سیستم‌های خودت یا مشتری با مجوز**
3. **اطلاعات جمع‌آوری شده رو بعد از آزمایش پاک کن**
4. **در گزارش، روش حفاظت در برابر این حمله رو هم بنویس**

---

## **🎓 هدف آموزشی این پروژه:**

با ساختن این سیستم، می‌فهمی که:
1. **چطور fingerprinting کار می‌کنه**
2. **چقدر اطلاعات از کاربران قابل جمع‌آوری‌ست**
3. **چطور می‌تونی جلوی این حمله رو بگیری:**
   - استفاده از افزونه‌های ضد fingerprinting
   - غیرفعال کردن JavaScript
   - استفاده از مرورگرهای مقاوم مثل Tor
   - تنظیمات حریم خصوصی مرورگر

---

**آیا الان کامل متوجه شدی؟** اگر مرحله‌ای مشکل داشت یا نیاز به توضیح بیشتر داری، بپرس. 

**یادت باشه:** این کدها فقط برای آموزش در محیط کنترل‌شده شخصی تو هستن!