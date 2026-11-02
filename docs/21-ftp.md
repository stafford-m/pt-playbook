# 21 - FTP

## Service Usage

Basic login
```bash
ftp $ip
```
```bash
lftp -u $un,$pw $ip
```
Standard Commands
```bash
PWD
LIST -la         # LIST -a surfaces dotfiles; many clients omit -a by default
NLST             # bare names only — sometimes allowed where LIST is filtered
STAT .           # same listing, control channel only
CWD /incoming    # enumerate common dirs by hand: /pub /upload /incoming /files /backup /www /home
CDUP
get
```

## 1. Banner Grab & Unauthenticated Enumeration

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

## 2. Authenticated Enumeration

### Basic commands on signed in
```bash
MKD .testdir                -> 257 = create allowed
CWD .testdir
STOR probe.txt              -> 150/226 = upload allowed  (use a harmless name)
APPE probe.txt              -> append allowed
SITE CHMOD 777 probe.txt    -> 200 = permission control
SIZE probe.txt              -> confirms it landed
DELE probe.txt              -> cleanup
RMD .testdir
```

Pull and grep
```bash
lftp -u anonymous,anonymous@ $IP -e "set ftp:ssl-allow no; mirror --parallel=2 / ./ftp_loot; bye"
grep -riE 'pass|pwd|user|key|token|secret' ftp_loot/
```

## 3. Password Spraying / Bruteforcing

Spray to test passwords
```bash
hydra -L users.txt -p 'Spring2026!' -t 4 -W 3 -o spray_pass1.txt ftp://$ip
```
f to exit on hit
```bash
hydra -L users.txt -p pass.txt -f -t 4 -W 3 -o spray_pass1.txt ftp://$ip
```

## 5. Quick Wins

TYPE I
```bash
TYPE I                     # <-- BEFORE upload. non-negotiable.
CWD /var/www/html
STOR s.php
SITE CHMOD 755 s.php
```

### Default Credentials:
```
anonymous:anonymous@
anonymous:anonymous
anonymous:<anything>
      ftp:ftp
      ftp:<email>
```

## 6. Config Paths

vsftpd (Very Secure FTP)	
```text
/etc/vsftpd.conf
/etc/vsftpd/vsftpd.conf
```

ProFTPD	
```text
/etc/proftpd/proftpd.conf
/etc/proftpd.conf
/usr/local/etc/proftpd.conf
```

Pure-FTPd	
```text
/etc/pure-ftpd/pure-ftpd.conf
/etc/pure-ftpd/conf/
```

IIS FTP
```text
%SystemDrive%\Windows\System32\inetsrv\config\applicationHost.config
```
FileZilla
```text
%ProgramData%\filezilla-server\settings.xml
%ProgramFiles%\FileZilla Server\FileZilla Server.xml
```