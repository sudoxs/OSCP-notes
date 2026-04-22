25

# use
```
nc target 25
```
VRFY admin
### SCAN:
Test-NetConnection -Port 25 192.168.50.8
nc -nv x.x.x.x 25
VRFY root

### SCRIPT:
##### python3 vrfy.py ==username== ==target_ip==
```python
#!/usr/bin/python

import socket
import sys

if len(sys.argv) != 3:
        print("Usage: vrfy.py ==username== ==target_ip==")
        sys.exit(0)

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

ip = sys.argv[2]
connect = s.connect((ip,25))

banner = s.recv(1024)

print(banner)

user = (sys.argv[1]).encode()
s.send(b'VRFY ' + user + b'\r\n')
result = s.recv(1024)

print(result)

s.close()
```

---
```python
#!/usr/bin/python3

import socket
import sys

if len(sys.argv) != 3:
    print("Usage: vrfy.py userlist.txt target_ip")
    sys.exit(0)

userlist = sys.argv[1]
ip = sys.argv[2]

with open(userlist, "r") as f:
    for line in f:
        user = line.strip()
        if not user:
            continue

        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.connect((ip, 25))

        banner = s.recv(1024)

        s.send(b'VRFY ' + user.encode() + b'\r\n')
        result = s.recv(1024)

        if b'252' in result:
            print(f"[+] VALID USER: {user}")
        elif b'550' in result:
            print(f"[-] INVALID USER: {user}")
        else:
            print(f"[?] UNKNOWN RESPONSE for {user}")

        s.close()
```



|Category|Command / Flag|Purpose (Short Explanation)|
|---|---|---|
|**Scan**|`nmap -p 25 IP`|Check if SMTP service is running|
|**Scan**|`nmap -p 25 --script smtp-commands IP`|Enumerate supported SMTP commands|
|**Scan**|`nmap -p 25 --script smtp-enum-users IP`|Test for valid users via SMTP|
|**Enum**|`smtp-user-enum -M VRFY -U users.txt -t IP`|Enumerate users using **VRFY**|
|**Enum**|`smtp-user-enum -M EXPN -U users.txt -t IP`|Enumerate mailing lists via **EXPN**|
|**Enum**|`smtp-user-enum -M RCPT -U users.txt -t IP`|Enumerate users via **RCPT TO**|
|**Manual**|`nc IP 25`|Manually interact with SMTP|
|**Manual**|`HELO test`|Identify yourself to SMTP server|
|**Manual**|`VRFY username`|Verify if a user exists|
|**Manual**|`EXPN listname`|Expand a mailing list|
|**Manual**|`MAIL FROM:<test@test.com>`|Start a mail transaction|
|**Manual**|`RCPT TO:<user@domain>`|Test if recipient exists|