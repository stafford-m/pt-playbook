# [PORT] - [PROTOCOL NAME]

## 1. Banner Grab & Manual Verification

netcat grab
```bash
nc -nv $IP 21
```
netcat grab with timeout
```bash
timeout 5 nc $IP 21
```

If SSL/TLS is enabled on this port (implicit/explicit ftps - implicit is normally on 990)
```bash
openssl s_client -connect $IP:21 -starttls ftp
```

curl - Works but noisy
```bash
curl -v ftp://$IP:21/ 2>&1 | head -n 5 
```

## 2. Unauthenticated Enumeration

Anonymous login: anonymous:anonymous
```bash
ftp $IP
```
```bash
lftp -u anonymous,anonymous@ $IP
```
```bash
curl -s --max-time 8 ftp://anonymous:anonymous@$IP/
```

Will report if root is writable
```bash
nmap -p 21 --script ftp-anon $IP
```

Enumerate FTP features
```bash
nmap -p 21 --script ftp-features $ip
```

enumerate common directories
```bash
gobuster dir -u ftp://$ip -w $wordlist
```

## 3. Authenticated Enumeration

## 4. Password Spraying / Bruteforcing

## 5. Attack Vectors & Shell Primatives

## 6. Quick Wins

### Default Credentials:
```
anonymous:anonymous@
anonymous:anonymous
anonymous:<anything>
      ftp:ftp
      ftp:<email>
```

## 7. Config Paths
