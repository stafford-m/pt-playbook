# FTP

_Port: 21_

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

**ALWAYS list with `-a` — dotfiles (`.htaccess`, `.bash_history`, `.ssh/`, backups) are the highest-yield FTP find.**
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

If SSL/TLS is enabled on this port (implicit/explicit ftps - implicit is normally on 990)
```bash
openssl s_client -connect $ip:21 -starttls ftp
```

Login over TLS (if plaintext refused or FTPS detected):
```bash
# Explicit FTPS (AUTH TLS)
lftp -u $un,$pw -e "set ftp:ssl-force true; set ftp:ssl-protect-data true; ls; bye" $ip
# Implicit FTPS (port 990 or given port)
lftp -u $un,$pw -e "set ftp:ssl-force true; set ftp:ssl-protect-data true; open ftps://$ip:990; ls; bye"
```