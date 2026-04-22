
host <DNS>
host -t mx <DNS>
for ip in $(cat <list>.txt); do host $ip.<DNS>; done
for ip in $(seq <A> <Z>); do host x.x.x.$ip; done | grep -v "not found"

dnsrecon -d <DNS> -t std
dnsrecon -d <DNS> -D < ~/list.txt > -t brt
-d  domain
-D  wordlist for subdomain
-t  brute

dnsenum <DNS>

xfreerdp /u:student /p:lab /v:192.168.50.152

nslookup <xxx.DNS>

nslookup -type=TXT <xxx.DNS> x.x.x.x
