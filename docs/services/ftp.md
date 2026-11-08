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

## Config Paths

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

## Post-Exploit / Pivot

- FTP to web-shell chain → see HTTP notes
- File disclosure → see Linux PrivEsc / TTY notes
- Configs found with a shell → grep for creds, reuse across services