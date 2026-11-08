curl - Works but noisy
```bash
curl -v ftp://$ip:21/ 2>&1 | head -n 5 
```

Anonymous login: anonymous:anonymous
```bash
curl -s --max-time 8 ftp://anonymous:anonymous@$ip/
```

Will report if root is writable
```bash
nmap -p 21 --script ftp-anon $ip
```

Enumerate FTP features
```bash
nmap -p 21 --script ftp-features $ip
```

enumerate common directories
```bash
gobuster dir -u ftp://$ip -w pslist.txt
```

All NSE scripts
```bash
nmap -p21 -sV --script "*ftp*" $ip
```

# Service Enumeration

```bash
nmap -sC -sV -p- $ip

sudo nmap -sU --open --top-ports=200 192.168.110.8

ls /usr/share/nmap/scripts | grep smtp-*

nmap --script-help smtp-*

nc -nvv -w 1 -z 192.168.50.152 3388-3390

nc -nv $ip 21

timeout 5 nc $ip 21

curl -s --max-time 8 ftp://anonymous:anonymous@$ip/

gobuster dir -u ftp://$ip -w pslist.txt

curl -v ftp://$ip:21/ 2>&1 | head -n 5 
```