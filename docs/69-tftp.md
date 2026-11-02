# 69 - TFTP

## Service Usage

Open interactive tftp session
```bash
tftp $ip
```
Download a file (interactive)
```bash
get file
```
Upload a file (interactive)
```bash
put file
```
Download a file (non-interactive)
```bash
tftp $ip -c get file
```
Upload a file (non-interactive)
```bash
tftp $ip -c put file
```

## 0. Fast Path (try these first)

Confirm TFTP is open (UDP)
```bash
nmap -p 69 -sU -sV $ip
```
Grab a common device config file
```bash
tftp $ip -c get startup-config
```

## 1. Banner Grab & Unauthenticated Enumeration

UDP version and default-script scan
```bash
nmap -p 69 -sU -sV -sC $ip
```

## 2. Authenticated Enumeration

No auth — grab known sensitive filenames (no LIST, so guess/brute filenames).
Get running-config
```bash
tftp $ip -c get running-config
```
Get startup-config
```bash
tftp $ip -c get startup-config
```
Get config.txt
```bash
tftp $ip -c get config.txt
```

## 3. Password Spraying / Bruteforcing

N/A — no auth. Skip.

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