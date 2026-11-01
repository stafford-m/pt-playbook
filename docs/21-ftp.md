# 21 - FTP

## 1. Banner Grab & Manual Verification

netcat grab
```bash
nc -nv $ip 21
```
netcat grab with timeout
```bash
timeout 5 nc $ip 21
```

If SSL/TLS is enabled on this port (implicit/explicit ftps - implicit is normally on 990)
```bash
openssl s_client -connect $ip:21 -starttls ftp
```

curl - Works but noisy
```bash
curl -v ftp://$ip:21/ 2>&1 | head -n 5 
```

## 2. Unauthenticated Enumeration

Anonymous login: anonymous:anonymous
```bash
ftp $ip
```
```bash
lftp -u anonymous,anonymous@ $ip
```
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
