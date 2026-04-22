161 

### SCAN:
sudo nmap -sU --open -p 161 x.x.x.1-254 
snmpwalk -c public -v1 target

dism /online /Enable-Feature /FeatureName:TelnetClient
telnet 192.168.50.8 25

### CONNECT:
echo public > community
echo private >> community
echo manager >> community
for ip in $(seq 1 254); do echo X.X.X.$ip; done > ips
onesixtyone -c community -i ips       => x.x.x.x

snmpwalk -c public -v1 -t 10 x.x.x.x

snmpwalk -c public -v1 x.x.x.x 1.3.6.1.4.1.77.1.2.25 [^1]

| توضیح                     | OID                    |
| ------------------------- | ---------------------- |
| لیست کاربران ویندوز       | 1.3.6.1.4.1.77.1.2.25  |
| پروسه‌های در حال اجرا     | 1.3.6.1.2.1.25.4.2.1.2 |
| نرم‌افزارهای نصب‌شده      | 1.3.6.1.2.1.25.6.3.1.2 |
| IPهای داخلی و Interfaceها | 1.3.6.1.2.1.4.20.1.1   |
| پورت‌های TCP باز          | 1.3.6.1.2.1.6.13.1.3   |
| جدول Routing دستگاه       | 1.3.6.1.2.1.4.21.1.1   |


[^1]: 
	 لیست کاربران ویندوز
	1.3.6.1.4.1.77.1.2.25
	
	پروسه‌های در حال اجرا
	1.3.6.1.2.1.25.4.2.1.2
	
	نرم‌افزارهای نصب‌شده
	1.3.6.1.2.1.25.6.3.1.2
	
	IPهای داخلی و Interfaceها
	1.3.6.1.2.1.4.20.1.1
	
	پورت‌های TCP باز
	1.3.6.1.2.1.6.13.1.3
	
	جدول Routing دستگاه
	1.3.6.1.2.1.4.21.1.1
	


| Category      | Command / Flag                          | Purpose (Short Explanation)            |
| ------------- | --------------------------------------- | -------------------------------------- |
| **Scan**      | `nmap -p 161 IP`                        | Check if SNMP is running               |
| **Scan**      | `nmap -sU -p 161 --script snmp-info IP` | Basic SNMP information                 |
| **Enum**      | `snmpwalk -v2c -c public IP`            | Enumerate SNMP using default community |
| **Enum**      | `snmpwalk -v1 -c public IP`             | Enumerate SNMP v1                      |
| **Enum**      | `snmpwalk -v2c -c private IP`           | Try alternative community string       |
| **Enum**      | `snmp-check IP`                         | Automated SNMP enumeration             |
| **Enum**      | `onesixtyone -c community.txt IP`       | Bruteforce SNMP community strings      |
| **Info Leak** | `snmpwalk ... 1.3.6.1.2.1.1`            | System info (hostname, OS)             |
| **Info Leak** | `snmpwalk ... 1.3.6.1.2.1.25.4.2.1.2`   | Running processes                      |
| **Info Leak** | `snmpwalk ... 1.3.6.1.2.1.25.6.3.1.2`   | Installed software                     |
| **Info Leak** | `snmpwalk ... 1.3.6.1.2.1.6.13.1.3`     | Network connections                    |