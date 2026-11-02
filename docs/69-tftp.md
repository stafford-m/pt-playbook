# 69 - TFTP

## Service Usage

Download/upload (no auth)
```bash
tftp $ip
get file
put file
```
Non-interactive
```bash
tftp $ip -c get file
tftp $ip -c put file
```

## 0. Fast Path (try these first)

Confirm + list (TFTP has no built-in listing, so this is about grabbing known files):
```bash
nmap -p 69 -sU -sV $ip
tftp $ip -c get startup-config        # routers/switches store configs
```

## 1. Banner Grab & Unauthenticated Enumeration

nmap
```bash
nmap -p 69 -sU -sV -sC $ip
```

## 2. Authenticated Enumeration

*No auth. What you can do is grab known sensitive filenames (no LIST, so guess/brute filenames):*
```bash
# Common device configs
tftp $ip -c get running-config
tftp $ip -c get startup-config
tftp $ip -c get config.txt
```

## 3. Password Spraying / Bruteforcing

*N/A — no auth. Skip.*

## 5. Quick Wins

### 5.1 Network device config dump
```bash
tftp $ip -c get startup-config    # creds, SNMP strings, routes
```

### 5.2 Write a file (if writable) — e.g., overwrite config
```bash
tftp $ip -c put malicious.bin
```

## 6. Config Paths

tftpd
```text
/etc/default/tftpd-hpa
/etc/xinetd.d/tftp
```

Config quick-reference:
```text
TFTP_DIRECTORY -> /srv/tftp or /tftpboot (what's served)
```

## 7. Post-Exploit / Pivot

- Config dump → creds → reuse elsewhere
- Writable TFTP → upload payload/firmware

### Common gotchas
```text
- UDP-based (69) and tiny blocks — use -c carefully; large transfers fail.
- No directory listing — you must know/guess filenames. Use wordlists of common device config names.
```