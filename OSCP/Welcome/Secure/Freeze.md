**آها! پس تو می‌خوای یه سیستم فریز/آنفریز داشته باشی که کنترل کامل داشته باشی. بیا برات یه سیستم حرفه‌ای بسازم:**

---

## **🎯 سیستم Freeze/Unfreeze کنترل شونده**

### **📁 ساختار فایل‌ها:**
```
/opt/freezer/
├── freezer.sh          # کنترل اصلی
├── freeze_state.sh     # فریز کردن
├── unfreeze_state.sh   # آنفریز کردن
├── status_check.sh     # وضعیت
└── frozen_snapshot/    # ذخیره حالت
```

---

## **🔧 نصب سیستم فریزر:**

### **فایل ۱: `install_freezer.sh`** (یکبار اجرا کن)
```bash
#!/bin/bash
# install_freezer.sh

echo "[*] نصب سیستم Freeze/Unfreeze..."

# ایجاد دایرکتوری
sudo mkdir -p /opt/freezer/frozen_snapshot
sudo chmod 700 /opt/freezer

# کپی تمام اسکریپت‌ها
sudo cp freezer.sh /opt/freezer/
sudo cp freeze_state.sh /opt/freezer/
sudo cp unfreeze_state.sh /opt/freezer/
sudo cp status_check.sh /opt/freezer/

# اجازه اجرا
sudo chmod +x /opt/freezer/*.sh

# ایجاد لینک سمبلیک برای دسترسی راحت
sudo ln -sf /opt/freezer/freezer.sh /usr/local/bin/freezer

# ایجاد systemd service برای auto-unfreeze
sudo tee /etc/systemd/system/freezer-resume.service << 'EOF'
[Unit]
Description=Resume frozen state after reboot
After=multi-user.target
ConditionPathExists=/opt/freezer/frozen_snapshot/.frozen

[Service]
Type=oneshot
ExecStart=/opt/freezer/unfreeze_state.sh --resume
RemainAfterExit=yes
StandardOutput=journal

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload

echo "[+] نصب کامل شد!"
echo "   دستورات:"
echo "   freezer freeze    - فریز سیستم"
echo "   freezer unfreeze  - آنفریز سیستم"
echo "   freezer status    - وضعیت"
echo "   freezer help      - راهنما"
```

---

## **🎮 کنترل اصلی:**

### **فایل ۲: `freezer.sh`** (کنترلر اصلی)
```bash
#!/bin/bash
# freezer.sh - کنترل سیستم فریز/آنفریز

FREEZER_DIR="/opt/freezer"
SNAPSHOT_DIR="$FREEZER_DIR/frozen_snapshot"
STATE_FILE="$SNAPSHOT_DIR/.frozen"

show_help() {
    echo "🔄 سیستم مدیریت حالت فریز/آنفریز"
    echo ""
    echo "دستورات:"
    echo "  freeze    - فریز کردن سیستم (ذخیره حالت فعلی)"
    echo "  unfreeze  - آنفریز کردن (بازگشت به حالت فریز شده)"
    echo "  status    - نمایش وضعیت فعلی"
    echo "  enable    - فعال کردن auto-resume بعد از ریبوت"
    echo "  disable   - غیرفعال کردن auto-resume"
    echo "  backup    - گرفتن بکاپ از حالت فعلی"
    echo "  restore   - بازگردانی از بکاپ"
    echo "  help      - نمایش این راهنما"
    echo ""
    echo "مثال:"
    echo "  sudo freezer freeze"
    echo "  sudo freezer unfreeze"
}

check_root() {
    if [ "$EUID" -ne 0 ]; then 
        echo "❌ این دستور نیاز به sudo دارد"
        exit 1
    fi
}

case "$1" in
    freeze|f)
        check_root
        echo "🧊 در حال فریز کردن سیستم..."
        "$FREEZER_DIR/freeze_state.sh"
        ;;
        
    unfreeze|u|thaw)
        check_root
        echo "🔥 در حال آنفریز کردن سیستم..."
        "$FREEZER_DIR/unfreeze_state.sh"
        ;;
        
    status|s)
        "$FREEZER_DIR/status_check.sh"
        ;;
        
    enable|e)
        check_root
        sudo systemctl enable freezer-resume.service
        echo "✅ Auto-resume بعد از ریبوت فعال شد"
        ;;
        
    disable|d)
        check_root
        sudo systemctl disable freezer-resume.service
        echo "✅ Auto-resume غیرفعال شد"
        ;;
        
    backup|b)
        check_root
        echo "💾 در حال گرفتن بکاپ..."
        tar -czf "/opt/freezer/backup_$(date +%Y%m%d_%H%M%S).tar.gz" "$SNAPSHOT_DIR" 2>/dev/null
        echo "✅ بکاپ در /opt/freezer/ ذخیره شد"
        ;;
        
    restore|r)
        check_root
        if [ -z "$2" ]; then
            echo "⚠️  لطفا نام فایل بکاپ را مشخص کنید"
            ls /opt/freezer/backup_*.tar.gz 2>/dev/null || echo "فایل بکاپی یافت نشد"
            exit 1
        fi
        echo "🔙 در حال بازگردانی از $2..."
        tar -xzf "$2" -C / 2>/dev/null
        echo "✅ بازگردانی کامل شد"
        ;;
        
    help|--help|-h)
        show_help
        ;;
        
    *)
        echo "⚠️  دستور نامعتبر: $1"
        show_help
        exit 1
        ;;
esac
```

---

## **🧊 فریزر اصلی:**

### **فایل ۳: `freeze_state.sh`**
```bash
#!/bin/bash
# freeze_state.sh - ذخیره حالت فعلی سیستم

SNAPSHOT_DIR="/opt/freezer/frozen_snapshot"
STATE_FILE="$SNAPSHOT_DIR/.frozen"

echo "[*] شروع فرآیند فریز..."

# پاکسازی snapshot قبلی
rm -rf "$SNAPSHOT_DIR" 2>/dev/null
mkdir -p "$SNAPSHOT_DIR"

# 1. ذخیره لیست پکیج‌ها
echo "[1] ذخیره لیست پکیج‌ها..."
dpkg --get-selections > "$SNAPSHOT_DIR/pkg_selections.list"
apt-mark showauto > "$SNAPSHOT_DIR/auto_pkgs.list"

# 2. ذخیره تنظیمات apt
echo "[2] ذخیره تنظیمات apt..."
cp -r /etc/apt/sources.list* "$SNAPSHOT_DIR/"
cp -r /etc/apt/trusted.gpg* "$SNAPSHOT_DIR/" 2>/dev/null
cp -r /etc/apt/preferences* "$SNAPSHOT_DIR/" 2>/dev/null

# 3. ذخیره cron jobs
echo "[3] ذخیره cron jobs..."
crontab -l > "$SNAPSHOT_DIR/crontab_backup" 2>/dev/null

# 4. ذخیره users و groups
echo "[4] ذخیره اطلاعات کاربران..."
getent passwd > "$SNAPSHOT_DIR/passwd.backup"
getent group > "$SNAPSHOT_DIR/group.backup"

# 5. ذخیره فایل‌های مهم سیستم
echo "[5] ذخیره فایل‌های مهم..."
IMPORTANT_FILES=(
    /etc/hosts
    /etc/hostname
    /etc/resolv.conf
    /etc/fstab
    /etc/network/interfaces
    /etc/ssh/sshd_config
    /etc/sudoers
    /etc/passwd
    /etc/group
    /etc/shadow-
    /etc/gshadow-
)

for file in "${IMPORTANT_FILES[@]}"; do
    if [ -f "$file" ]; then
        cp --parents "$file" "$SNAPSHOT_DIR/" 2>/dev/null
    fi
done

# 6. ذخیره وضعیت systemd services
echo "[6] ذخیره وضعیت سرویس‌ها..."
systemctl list-unit-files --type=service --state=enabled > "$SNAPSHOT_DIR/enabled_services.list"

# 7. ایجاد فایل وضعیت
echo "FROZEN_AT=$(date +%s)" > "$STATE_FILE"
echo "FROZEN_BY=$(whoami)" >> "$STATE_FILE"
echo "HOSTNAME=$(hostname)" >> "$STATE_FILE"

# 8. فعال کردن auto-resume
systemctl enable freezer-resume.service >/dev/null 2>&1

echo "[✅] سیستم فریز شد!"
echo "📊 اطلاعات ذخیره شده در: $SNAPSHOT_DIR"
echo "🔁 بعد از ریستارت، سیستم به این حالت برمی‌گردد"
echo ""
echo "برای غیرفعال کردن: sudo freezer disable"
echo "برای آنفریز دستی: sudo freezer unfreeze"
```

---

## **🔥 آنفریزر اصلی:**

### **فایل ۴: `unfreeze_state.sh`**
```bash
#!/bin/bash
# unfreeze_state.sh - بازگشت به حالت فریز شده

SNAPSHOT_DIR="/opt/freezer/frozen_snapshot"
STATE_FILE="$SNAPSHOT_DIR/.frozen"

# بررسی اینکه حالت فریز شده وجود دارد
if [ ! -f "$STATE_FILE" ]; then
    echo "⚠️  حالت فریز شده‌ای یافت نشد!"
    exit 1
fi

echo "[*] بازگردانی سیستم به حالت فریز شده..."

# پارامتر --resume برای حالت خودکار بعد از ریبوت
if [ "$1" != "--resume" ]; then
    read -p "⚠️  آیا مطمئن هستید؟ (y/N): " -n 1 -r
    echo
    if [[ ! $REPLY =~ ^[Yy]$ ]]; then
        echo "❌ عملیات لغو شد"
        exit 0
    fi
fi

# 1. بازگردانی پکیج‌ها
if [ -f "$SNAPSHOT_DIR/pkg_selections.list" ]; then
    echo "[1] بازگردانی پکیج‌ها..."
    dpkg --clear-selections
    dpkg --set-selections < "$SNAPSHOT_DIR/pkg_selections.list"
    
    # بازگردانی auto-marked packages
    if [ -f "$SNAPSHOT_DIR/auto_pkgs.list" ]; then
        while read pkg; do
            apt-mark auto "$pkg" 2>/dev/null
        done < "$SNAPSHOT_DIR/auto_pkgs.list"
    fi
    
    apt-get update
    apt-get dselect-upgrade -y
fi

# 2. بازگردانی تنظیمات apt
echo "[2] بازگردانی تنظیمات apt..."
cp -r "$SNAPSHOT_DIR/etc/apt/" /etc/ 2>/dev/null || true

# 3. بازگردانی cron jobs
if [ -f "$SNAPSHOT_DIR/crontab_backup" ]; then
    echo "[3] بازگردانی cron jobs..."
    crontab "$SNAPSHOT_DIR/crontab_backup" 2>/dev/null || true
fi

# 4. بازگردانی فایل‌های سیستم
echo "[4] بازگردانی فایل‌های سیستم..."
for file in "$SNAPSHOT_DIR"/etc/*; do
    if [ -f "$file" ]; then
        target="/${file#$SNAPSHOT_DIR}"
        cp "$file" "$target" 2>/dev/null && echo "  ↪ $target"
    fi
done

# 5. بازگردانی سرویس‌ها
if [ -f "$SNAPSHOT_DIR/enabled_services.list" ]; then
    echo "[5] بازگردانی سرویس‌ها..."
    # غیرفعال کردن همه سرویس‌ها (به جز essential)
    systemctl list-unit-files --type=service --state=enabled | grep enabled | awk '{print $1}' | 
        while read service; do
            case $service in
                ssh|systemd-*|dbus-*|network-*|rsyslog|fstrim|apt-daily*)
                    # این سرویس‌ها رو دست نزن
                    ;;
                *)
                    systemctl disable "$service" 2>/dev/null || true
                    ;;
            esac
        done
    
    # فعال کردن سرویس‌های ذخیره شده
    grep -E 'enabled$' "$SNAPSHOT_DIR/enabled_services.list" | awk '{print $1}' |
        while read service; do
            systemctl enable "$service" 2>/dev/null || true
        done
fi

# 6. پاکسازی state file (اختیاری)
if [ "$1" != "--resume" ]; then
    echo "[6] پاکسازی حالت فریز..."
    rm -f "$STATE_FILE"
    systemctl disable freezer-resume.service
fi

echo "[✅] بازگردانی کامل شد!"
echo "🔄 برای اعمال کامل تغییرات، سیستم ریستارت شود:"
echo "   sudo reboot"
```

---

## **📊 نمایش وضعیت:**

### **فایل ۵: `status_check.sh`**
```bash
#!/bin/bash
# status_check.sh - نمایش وضعیت سیستم فریزر

STATE_FILE="/opt/freezer/frozen_snapshot/.frozen"
SNAPSHOT_DIR="/opt/freezer/frozen_snapshot"

echo "🔄 وضعیت سیستم Freeze/Unfreeze"
echo "═════════════════════════════════"

# بررسی وجود حالت فریز
if [ -f "$STATE_FILE" ]; then
    echo "🧊 حالت: FROZEN"
    echo ""
    
    # خواندن اطلاعات فریز
    while IFS='=' read -r key value; do
        case $key in
            FROZEN_AT)
                frozen_date=$(date -d "@$value" 2>/dev/null || echo "$value")
                echo "📅 زمان فریز: $frozen_date"
                ;;
            FROZEN_BY)
                echo "👤 فریز شده توسط: $value"
                ;;
            HOSTNAME)
                echo "🖥️  میزبان: $value"
                ;;
        esac
    done < "$STATE_FILE"
    
    # اطلاعات snapshot
    echo ""
    echo "📊 اطلاعات ذخیره شده:"
    echo "  📦 پکیج‌ها: $(wc -l < "$SNAPSHOT_DIR/pkg_selections.list" 2>/dev/null || echo 0) پکیج"
    echo "  ⚙️  سرویس‌ها: $(grep -c enabled "$SNAPSHOT_DIR/enabled_services.list" 2>/dev/null || echo 0) سرویس فعال"
    echo "  📁 حجم: $(du -sh "$SNAPSHOT_DIR" 2>/dev/null | cut -f1)"
    
    # وضعیت auto-resume
    if systemctl is-enabled freezer-resume.service >/dev/null 2>&1; then
        echo "  🔄 Auto-resume: فعال ✅"
    else
        echo "  🔄 Auto-resume: غیرفعال ⭕"
    fi
    
else
    echo "🔥 حالت: UNFROZEN"
    echo "سیستم در حالت عادی است"
fi

echo ""
echo "📍 مکان snapshot: $SNAPSHOT_DIR"
echo "💡 دستورات:"
echo "  sudo freezer freeze    - فریز سیستم"
echo "  sudo freezer unfreeze  - بازگشت به حالت فریز"
echo "  sudo freezer status    - نمایش این صفحه"
```

---

## **🚀 نصب سریع (همه در یک فایل):**

### **فایل ۶: `one_click_install.sh`**
```bash
#!/bin/bash
# one_click_install.sh - نصب کامل با یک کلیک

echo "📦 نصب سیستم Freeze/Unfreeze..."
echo "این سیستم به شما امکان می‌دهد:"
echo "  1. سیستم را فریز کنید (ذخیره حالت فعلی)"
echo "  2. هر وقت خواستید به حالت فریز برگردید"
echo "  3. حالت auto-resume را فعال/غیرفعال کنید"
echo ""

# ایجاد دایرکتوری
sudo mkdir -p /opt/freezer/frozen_snapshot

# دانلود اسکریپت‌ها از GitHub (یا کپی از محل فعلی)
cd /opt/freezer

# ایجاد فایل‌ها
cat > freezer.sh << 'EOF'
# محتوای freezer.sh اینجا کپی شود
EOF

cat > freeze_state.sh << 'EOF'
# محتوای freeze_state.sh اینجا کپی شود
EOF

cat > unfreeze_state.sh << 'EOF'
# محتوای unfreeze_state.sh اینجا کپی شود
EOF

cat > status_check.sh << 'EOF'
# محتوای status_check.sh اینجا کپی شود
EOF

# تنظیم مجوزها
sudo chmod +x /opt/freezer/*.sh
sudo ln -sf /opt/freezer/freezer.sh /usr/local/bin/freezer

echo "✅ نصب کامل شد!"
echo ""
echo "🎮 دستورات اصلی:"
echo "  sudo freezer freeze    # فریز سیستم"
echo "  sudo freezer unfreeze  # بازگشت به حالت فریز"
echo "  sudo freezer status    # وضعیت"
echo ""
echo "🔧 برای تست:"
echo "  1. اول: sudo freezer status"
echo "  2. سپس: sudo freezer freeze"
echo "  3. تغییراتی ایجاد کنید (مثلاً apt install چیزی)"
echo "  4. سپس: sudo freezer unfreeze"
echo "  5. ببینید همه چیز برمی‌گردد!"
```

---

## **🎯 مثال عملی استفاده:**

### **سناریو ۱: فریز کردن، آزمایش، برگشت**
```bash
# ۱. بررسی وضعیت اولیه
sudo freezer status

# ۲. فریز کردن
sudo freezer freeze

# ۳. آزمایش‌های خطرناک!
sudo apt install metasploit
sudo useradd testhacker
sudo systemctl enable apache2

# ۴. برگشت به حالت فریز
sudo freezer unfreeze
# یا ریستارت کن:
sudo reboot  # سیستم خودش آنفریز می‌شه
```

### **سناریو ۲: فریز کن، بعداً برگرد**
```bash
# امروز فریز می‌کنم
sudo freezer freeze
sudo freezer enable  # فعال کردن auto-resume

# فردا کارهای مختلف می‌کنم...
# وقتی خواستم به دیروز برگردم:
sudo reboot  # یا
sudo freezer unfreeze
```

---

## **⚙️ حالت‌های کاری:**

### **حالت ۱: دستی (Manual)**
```bash
sudo freezer freeze      # ذخیره
# ... کارها ...
sudo freezer unfreeze    # برگشت
```

### **حالت ۲: خودکار بعد از ریبوت (Auto-resume)**
```bash
sudo freezer freeze
sudo freezer enable     # فعال کردن
sudo reboot             # بعد ریبوت خودش برمی‌گرده
```

### **حالت ۳: موقت (فقط پکیج‌ها)**
```bash
# فریز کن
sudo freezer freeze

# غیرفعال کن تا بعد ریبوت برنگرده
sudo freezer disable

# وقتی خواستی برگردی:
sudo freezer unfreeze
```

---

## **🔒 امنیت و نکات:**

### **فایل backup برای حالت فریز:**
```bash
# بکاپ بگیر
sudo freezer backup

# لیست بکاپ‌ها
ls -la /opt/freezer/backup_*.tar.gz

# بازگردانی
sudo freezer restore /opt/freezer/backup_20241225_143022.tar.gz
```

### **پاکسازی:**
```bash
# حذف کامل سیستم فریزر
sudo rm -rf /opt/freezer
sudo rm -f /usr/local/bin/freezer
sudo systemctl disable freezer-resume.service
sudo rm -f /etc/systemd/system/freezer-resume.service
sudo systemctl daemon-reload
```

---

## **✅ خلاصه دستورات:**
```
🧊 فریز:          sudo freezer freeze
🔥 آنفریز:        sudo freezer unfreeze
📊 وضعیت:         sudo freezer status
🔛 فعال‌سازی:     sudo freezer enable  
🔘 غیرفعال‌سازی:  sudo freezer disable
💾 بکاپ:          sudo freezer backup
🔙 بازگردانی:     sudo freezer restore <file>
❓ راهنما:        freezer help
```

---

**الان کامل متوجه شدی؟ این سیستم دقیقاً همون چیزیه که می‌خواستی: فریز/آنفریز کنترل شونده با امکان ریستارت خودکار!**