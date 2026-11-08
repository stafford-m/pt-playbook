# RDP - 3389

## Service Usage

Connect with clipboard sharing
```bash
xfreerdp /v:$ip /u:$un /p:$pw +clipboard
```
Connect with drive and clipboard sharing (file transfer)
```bash
xfreerdp /v:$ip /u:$un /p:$pw /cert:ignore +drives:/tmp,/share
```
Connect with rdesktop
```bash
rdesktop $ip
```

## 0. Fast Path (try these first)

Auth check without a full session
```bash
nxc rdp $ip -u $un -p $pw
```
Connect with the most reliable client
```bash
xfreerdp /v:$ip /u:$un /p:$pw /cert:ignore
```

## 1. Banner Grab & Unauthenticated Enumeration

Version, NLA, and encryption check
```bash
nmap -p 3389 -sV -sC $ip
```
NTLM info leak via RDP
```bash
nmap -p 3389 --script rdp-ntlm-info $ip
```

## 2. Authenticated Enumeration

Enumerate current user and privileges
```bash
whoami /all
```
Full system information dump
```bash
systeminfo
```
List domain users
```bash
net user /domain
```
List local Administrators group
```bash
net localgroup Administrators
```

## 3. Password Spraying / Bruteforcing

RDP is slow to spray — prefer SMB/WinRM for cred validation, then use found creds here.
Spray with netexec (lightweight check)
```bash
nxc rdp $ip -u users.txt -p 'Spring2026!' --continue-on-success
```
Brute force with hydra
```bash
hydra -L users.txt -P pass.txt rdp://$ip
```

## 5. Quick Wins

### 5.1 Creds → login
```bash
xfreerdp /v:$ip /u:$un /p:$pw /cert:ignore /dynamic-resolution
```

### 5.2 NLA disabled / older target → may bypass password for some ciphers
```bash
nmap -p 3389 --script rdp-enum-encryption $ip
```

### 5.3 Credentials stored in RDP manager / saved creds (post-exploit privesc)
```bash
# Look for .rdp files / cmdkey entries — see Windows PrivEsc notes
cmdkey /list
```

### 5.4 Clipboard + drive share for file transfer
```bash
xfreerdp /v:$ip /u:$un /p:$pw +clipboard +drives:/tmp,/share
```

## 6. Config Paths

RDP settings
```text
HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\
HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\
```

Config quick-reference:
```text
fDenyTSConnections -> 0 = RDP enabled
SecurityLayer -> RDP vs SSL
UserAuthentication -> NLA on/off
```

## 7. Post-Exploit / Pivot

- GUI access → easier file transfer / kernel exploit prep
- Same creds → SMB/WinRM notes
- RDP saved creds → credential theft (see Windows PrivEsc notes)

### Common gotchas
```text
- NLA = need valid creds before you see a login prompt. No creds? Enum is limited.
- CredSSP / NLA errors with xfreerdp? Try /sec:nla or /sec:rdp to adjust.
- Clipboard not working? Reconnect with +clipboard.
- RDP slow for brute — never brute RDP first; validate via SMB/WinRM then log in once.
```