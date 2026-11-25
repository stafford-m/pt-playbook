# SMTP

_Port: 25_

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

# smtp.py

Check Resources for script

Validate Usernames (sometimes error codes can give it away)
```bash
python3 smtp.py root 192.168.50.8
python3 smtp.py johndoe 192.168.50.8
```