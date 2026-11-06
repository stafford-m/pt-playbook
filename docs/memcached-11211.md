# Memcached - 11211

## Service Usage

Connect with nc
```bash
nc -nv $ip 11211
```

## 0. Fast Path (try these first)

Test access and dump stats
```bash
echo "stats" | nc $ip 11211
```
Dump item stats (to find slabs)
```bash
echo "stats items" | nc $ip 11211
```

## 1. Banner Grab & Unauthenticated Enumeration

Version and default-script scan
```bash
nmap -p 11211 -sV -sC $ip
```
Memcached-info NSE script
```bash
nmap -p 11211 --script memcached-info $ip
```
Query version manually
```bash
printf "version\r\n" | nc $ip 11211
```
Query stats manually
```bash
printf "stats\r\n" | nc $ip 11211
```

## 2. Authenticated Enumeration

Memcached (old versions) has no auth — extract cached data here.
Dump item stats
```bash
echo "stats items" | nc $ip 11211
```
Dump a specific slab (replace placeholders)
```bash
echo "stats cachedump <slab_id> <limit>" | nc $ip 11211
```
Automate full dump
```bash
memcdump --servers=$ip
```

## 3. Password Spraying / Bruteforcing

N/A — no auth (unless SASL, which is rare).

## 5. Quick Wins

### 5.1 Cache contains app secrets
```bash
memcdump --servers=$ip | grep -iE 'pass|token|key|session|user'
```

## 6. Config Paths

```text
/etc/memcached.conf
```

Config quick-reference:
```text
-u root -> running as root (but memcached is just a cache, no RCE directly)
-l 0.0.0.0 -> listening externally
```

## 7. Post-Exploit / Pivot

- Cached session tokens → hijack sessions (see HTTP notes)
- Cached creds → reuse elsewhere

### Common gotchas
```text
- No RCE from memcached alone — it's a data/cache leak vector.
- Older memcached (<1.4.14?) had no SASL — open read. Newer may need SASL.
```