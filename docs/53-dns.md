# 53 - DNS

## Service Usage

Query
```bash
dig @$ip $domain ANY
dig @$ip $domain A
dig @$ip $domain MX
dig @$ip $domain TXT
```
Reverse lookup
```bash
dig @$ip -x $ip
```
Zone transfer
```bash
dig @$ip $domain AXFR
```

## 0. Fast Path (try these first)

Zone transfer (free full zone dump if misconfigured):
```bash
dig @$ip $domain AXFR
```

If that fails, query-brute:
```bash
dnsrecon -d $domain -t axfr
dnsrecon -d $domain -t std
```

## 1. Banner Grab & Unauthenticated Enumeration

Version
```bash
dig @$ip CH TXT version.bind
```

Zone transfer + standard recon
```bash
dnsrecon -d $domain -t axfr -t std -t brt
dnsenum $domain
```

nmap NSE
```bash
nmap -p 53 -sV -sC $ip
nmap -p 53 --script dns-zone-transfer $ip
```

Subdomain brute
```bash
dnsrecon -d $domain -t brt -D /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
```
```bash
ffuf -u http://$ip/ -H "Host: FUZZ.$domain" -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -fs <size>
```

## 2. Authenticated Enumeration

*DNS has no auth — "authenticated" here means once you're on the box, read local DNS configs for internal-only names.*

Read local configs (post-exploit)
```bash
cat /etc/hosts
cat /etc/resolv.conf
cat /etc/bind/named.conf
```

## 3. Password Spraying / Bruteforcing

*N/A — DNS has no password auth. Skip.*

## 5. Quick Wins

### 5.1 AXFR worked → full zone dump
```bash
dig @$ip $domain AXFR
```

### 5.2 Find internal hostnames → pivot targets
```bash
# Zone dump reveals intranet boxes not in your scope list — check and hit them
```

### 5.3 TXT/SPF records leak IPs and mail infrastructure
```bash
dig @$ip $domain TXT
```

## 6. Config Paths

BIND
```text
/etc/bind/named.conf
/etc/bind/named.conf.local
/etc/bind/db.$domain
```
dnsmasq
```text
/etc/dnsmasq.conf
/etc/hosts
```

Config quick-reference:
```text
named.conf.local -> zone definitions, allow-transfer (AXFR rules)
/etc/hosts       -> internal name→IP map (great loot)
```

## 7. Post-Exploit / Pivot

- Zone/subdomain discovery → new HTTP/SMB targets
- /etc/hosts reveals internal assets
- DNS tunneling (rare on OSCP, skip unless hinted)

### Common gotchas
```text
- AXFR blocked? Try each NS record + the authoritative nameserver IPs directly.
- dig returns "connection timed out"? DNS may be TCP-only or behind firewall — try dig +tcp.
- version.bind often disabled — don't burn time on it.
- Subdomain brute is slow — start small wordlist, only grow if needed.
```