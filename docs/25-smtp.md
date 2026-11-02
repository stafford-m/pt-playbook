# 25 - SMTP

## Service Usage

Connect manually (control channel)
```bash
nc -nv $ip 25
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

## 0. Fast Path (try these first)

User enumeration — the main reason you'll touch SMTP on OSCP. Two methods, do both:

```bash
# Method 1: RCPT TO (most reliable — works even when VRFY/EXPN are disabled)
nc -nv $ip 25 <<EOF
HELO test.com
MAIL FROM:<test@test.com>
RCPT TO:<root@$ip>
RCPT TO:<admin@$ip>
QUIT
EOF
```
```bash
# Method 2: automated via smtp-user-enum
smtp-user-enum -M RCPT -U users.txt -t $ip
smtp-user-enum -M VRFY -U users.txt -t $ip
```

Open relay check (a relay lets you send from anywhere — also exploitable for internal-to-external flow):
```bash
smtp-user-enum -M RCPT -U users.txt -t $ip    # separate concern, but note any 250 to all
```

## 1. Banner Grab & Unauthenticated Enumeration

netcat grab
```bash
nc -nv $ip 25
timeout 5 nc $ip 25
```

If SSL/TLS / STARTTLS is required (usually port 465 implicit, 25 with STARTTLS):
```bash
openssl s_client -connect $ip:465 -crlf          # implicit TLS
openssl s_client -connect $ip:25 -starttls smtp -crlf   # explicit STARTTLS
```

nmap banner + version
```bash
nmap -p 25 -sV -sC $ip
```

nmap NSE scripts
```bash
nmap -p 25 --script smtp-commands $ip      # EHLO reply — reveals VRFY/EXPN enabled, auth methods, size limits
nmap -p 25 --script smtp-enum-users $ip
nmap -p 25 --script smtp-open-relay $ip
```

Manual command capability probe
```bash
nc -nv $ip 25
EHLO test.com
VRFY root        # 252/250 = VRFY enabled, 502 = disabled
EXPN root        # 250 = enabled
```

## 2. Authenticated Enumeration

*Auth is rarely the point on OSCP SMTP — you usually have no creds. If you do (AUTH LOGIN/PLAIN offered), you can use it to relay, enumerate internal users, or read mail via a mail client.*

Enumerate internal users when authenticated (sends mail to reveal valid addresses)
```bash
smtp-user-enum -M RCPT -U users.txt -t $ip
```

Auth support check (base64 the creds for AUTH LOGIN/PLAIN)
```bash
nc -nv $ip 25
EHLO test.com
AUTH LOGIN            # 334 = supported; then send base64(user), base64(pass)
```

Read mailbox (if creds are valid and POP3/IMAP is also open):
```bash
telnet $ip 110        # POP3
USER $user
PASS $pw
LIST
RETR 1
```

Check if the mail server accepts and forwards (possible intel/relay)
```bash
nc -nv $ip 25 <<EOF
EHLO test.com
MAIL FROM:<me@test.com>
RCPT TO:<target@$ip>
DATA
Subject: test

probe
.
QUIT
EOF
```

## 3. Password Spraying / Bruteforcing

*Last resort — SMTP brute force is rare on OSCP. Only after you've tried null/default and gathered usernames.*

Spray usernames against a password
```bash
hydra -L users.txt -p 'Spring2026!' -t 4 smtp://$ip
```

Brute force
```bash
hydra -L users.txt -P pass.txt -t 4 smtp://$ip
```

## 5. Quick Wins

### 5.1 Most common OSCP SMTP win — enumerate usernames, then reuse on other services
```bash
smtp-user-enum -M RCPT -U /usr/share/wordlists/SecLists/Usernames/Names/names.txt -t $ip > smtp_users.txt
# → feed smtp_users.txt into SSH/SMB/WinRM/HTTP password spraying
```

### 5.2 Open relay → phish/forward intel (rare but valid):
```bash
nmap -p 25 --script smtp-open-relay $ip
# If open: can send arbitrary mail, sometimes used for internal-to-external or spoofing.
```

### 5.3 Version-specific exploits (rare on OSCP but quick to check):
```bash
# Check version string in banner, then searchsploit for <version>
searchsploit <mail-server-name> <version>
```

### Default Credentials:
```
(none meaningful for SMTP itself — SMTP auth creds reuse from other services)
```

## 6. Config Paths

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

## 7. Post-Exploit / Pivot

- Harvested usernames → spray across SSH/SMB/WinRM/HTTP (see those notes)
- Valid mailbox creds + POP3/IMAP open → read mail for further creds (see POP3/IMAP notes)
- SMTP open relay → internal phishing / email-based RCE chain (rare)

### Common gotchas
```text
- VRFY/EXPN disabled? Use RCPT TO — it's the reliable fallback and nearly always tells you 250 vs 550.
- Responses vary: 250/251/252 = exists, 550/551/553 = no such user, 450 = graylisting/temp.
- EHLO may need the correct domain — try $ip, localhost, or the actual target domain if EHLO is rejected.
- STARTTLS on 25 vs implicit TLS on 465 — get the port right or you'll get no banner.
- smtp-user-enum needs valid FROM — if it errors, add the target domain or use any legit-looking sender.
```