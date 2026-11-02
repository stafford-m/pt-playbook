# 23 - Telnet

## Service Usage

Connect with telnet
```bash
telnet $ip
```
Connect with nc
```bash
nc -nv $ip 23
```

## 0. Fast Path (try these first)

Grab banner with timeout before trying default creds
```bash
timeout 5 nc $ip 23
```
Default creds: `admin:admin`, `root:root`, `cisco:cisco`, `admin:`, `root:password`.

## 1. Banner Grab & Unauthenticated Enumeration

Manual banner grab
```bash
nc -nv $ip 23
```
Banner grab with timeout
```bash
timeout 5 nc $ip 23 | head
```
Version and default-script scan
```bash
nmap -p 23 -sV -sC $ip
```
Telnet encryption and NTLM info scripts
```bash
nmap -p 23 --script telnet-encryption,telnet-ntlm-info $ip
```

## 2. Authenticated Enumeration

Enumerate a *nix-like host
```bash
uname -a; id; cat /etc/passwd
```
Enumerate a network device running-config
```bash
show running-config
```
Enumerate a network device version
```bash
show version
```

## 3. Password Spraying / Bruteforcing

Brute force with hydra
```bash
hydra -L users.txt -P pass.txt telnet://$ip
```

## 5. Quick Wins

### 5.1 Default creds on network devices
```bash
telnet $ip
# cisco:cisco, admin:admin, root:root, admin:(blank), admin:password
```

### 5.2 Embedded device → firmware/config dump
```bash
# after login, dump config for creds/keys/backdoors
show running-config
```

### 5.3 Linux host via telnet (rare) → treat like SSH but plaintext
```bash
# creds you already have may reuse here
```

## 6. Config Paths

telnetd (Linux, if xinetd/inetd)
```text
/etc/inetd.conf
/etc/xinetd.d/telnet
```

## 7. Post-Exploit / Pivot

- Device creds → try same creds on other services
- Config dump → find internal IPs, SNMP strings, other creds

### Common gotchas
```text
- Telnet = plaintext; creds sniffable if you're MITM (rare on OSCP).
- Network devices (routers/switches) are the main telnet targets on exams.
- No encryption — don't expect SSH-like behaviour.
```