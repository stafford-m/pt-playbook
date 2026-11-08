# SMTP - 25

## Service Usage

Connect manually (control channel)
```bash
nc -nv $ip 25
```

With SSL
```bash
openssl s_client -connect $ip:25 -starttls smtp -crlf
openssl s_client -connect $ip:465 -crlf
```
Standard Commands (inside the session)
```bash
EHLO $domain        # extended hello — lists supported commands (preferred over HELO)
HELO $domain        # basic hello
VRFY $user          # verify if $user exists (often disabled)
EXPN $list          # expand mailing list / alias (often disabled)
MAIL FROM:<$me>     # begin transaction
RCPT TO:<$user>     # recipient check — 250 = exists, 550 = no such user (best enum trick)
DATA                # begin message body
QUIT
```

## Service Enumeration

```bash
smtp-user-enum -M $command -U users.txt -t $ip
```

Use nmap script library
```bash
nmap --script-help smtp-*
```

## Config Paths

Postfix
```text
/etc/postfix/main.cf
/etc/postfix/master.cf
```
Sendmail
```text
/etc/mail/sendmail.cf
/etc/mail/access
```
Exim
```text
/etc/exim/exim.conf
/etc/exim4/exim4.conf.template
```

Config quick-reference:
```text
main.cf       -> check mynetworks = (relay rules), relayhost, virtual_alias_maps
access        -> check RELAY entries (who can relay)
exim.conf     -> check trusted_users / ACLs
```

## Post-Enumeration

- Harvested usernames → spray across SSH/SMB/WinRM/HTTP (see those notes)
- Valid mailbox creds + POP3/IMAP open → read mail for further creds

### Common gotchas
```text
- VRFY/EXPN disabled? Use RCPT TO — it's the reliable fallback and nearly always tells you 250 vs 550.
- Responses vary: 250/251/252 = exists, 550/551/553 = no such user, 450 = graylisting/temp.
- EHLO may need the correct domain — try $ip, localhost, or the actual target domain if EHLO is rejected.
- STARTTLS on 25 vs implicit TLS on 465 — get the port right or you'll get no banner.
- smtp-user-enum needs valid FROM — if it errors, add the target domain or use any legit-looking sender.
```