```
theHarvester -d DOMAIN -b SOURCE
```

| فلگ  | توضیح                |
| ---- | -------------------- |
| `-d` | دامنه                |
| `-b` | منبع (google, bing…) |
| `-l` | تعداد نتایج          |
| `-f` | خروجی HTML/XML       |
| `-s` | شروع از نتیجه خاص    |
| `-v` | verbose              |
```
theHarvester -d target.com -b all -l 500 -f result.html
```

---
theHarvester -d example.com -b google -l 100 -s 0
theHarvester -d example.com -b google -l 100 -s 100
theHarvester -d example.com -b google -l 100 -s 200

---

