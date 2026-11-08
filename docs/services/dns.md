# DNS - 53

## Service Scanning
Check both udp/53 and tcp/53
```bash
nmap -sS -sU -p T:53,U:53 $ip
```
nmap scripts that can do a lot of this
```bash
nmap --script-help dns-*
```
## Service Enumeration
Getting records
```bash
dig @$ip $domain ANY
```

```bash
host -t $rtype $url
```

using windows
```bash
nslookup -type=TXT $url $dnsserver
```

Resolve host to ip
```bash
host $url
```

standard scan
```bash
dnsrecon -d megacorpone.com -t std
```

bruteforce scan
```bash
dnsrecon -d megacorpone.com -t brt
```

autoenum most things
```bash
dnsenum $url
```

Attempt zone transfer
```bash
dig @$ip $domain AXFR
```

### Record Types
```text	
- NS: Nameserver records contain the name of the authoritative servers hosting the DNS records for a domain.
- A: Also known as a host record, the "a record" contains the IPv4 address of a hostname.
- AAAA: Also known as a quad A host record, the "aaaa record" contains the IPv6 address of a hostname.
- MX: Mail Exchange records contain the names of the servers responsible for handling email for the domain. A domain can contain multiple MX records.
- PTR: Pointer Records are used in reverse lookup zones and can find the records associated with an IP address.
- CNAME: Canonical Name Records are used to create aliases for other host records.
- TXT: Text records can contain any arbitrary data and be used for various purposes, such as domain ownership verification.
```

## Config Paths

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

## Post-Enumeration

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