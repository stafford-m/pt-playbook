# RPC

### Port: 111

## Service Usage

Query registered RPC services
```bash
rpcinfo -p $ip
```
Query a specific program (filter for nfs)
```bash
rpcinfo -p $ip | grep nfs
```

## 0. Fast Path (try these first)

List everything rpcbind knows about
```bash
rpcinfo -p $ip
```
This reveals NFS, mountd, rusersd, and other RPC services + their ports — often the only enum you need.

## 1. Banner Grab & Unauthenticated Enumeration

Version and default-script scan
```bash
nmap -p 111 -sV -sC $ip
```
nmap rpcinfo script
```bash
nmap -p 111 --script rpcinfo $ip
```
Full RPC dump (all programs/versions/ports)
```bash
rpcinfo -p $ip
```
Query SMB/MSRPC via rpcclient (if applicable)
```bash
rpcclient -U "" -N $ip -c "srvinfo"
```

## 2. Authenticated Enumeration

RPC itself has no auth — but rusersd/rstatd can leak user info.
List logged-in users via rusers
```bash
rusers $ip
```
Uptime/perf info via rstat (rare)
```bash
rstat $ip
```

## 3. Password Spraying / Bruteforcing

N/A — RPC has no password auth. Skip.

## 5. Quick Wins

### 5.1 rpcinfo reveals hidden services
```bash
rpcinfo -p $ip        # find NFS/mountd/other ports not in your initial scan
```

### 5.2 NFS exposed → go to NFS notes
```bash
showmount -e $ip
```

### 5.3 rusersd → usernames for spraying
```bash
rusers $ip
```

## 6. Config Paths

rpcbind
```text
/etc/rpc        # maps RPC program numbers to names
/etc/inetd.conf # which RPC services are enabled
```

## 7. Post-Exploit / Pivot

- Found NFS → NFS notes
- Found MSRPC → SMB notes
- rusersd → usernames → spray

### Common gotchas
```text
- 111 alone is useless; its whole value is pointing you to NFS/mountd/other RPC services.
- rpcinfo vs nmap --script rpcinfo — both fine, rpcinfo is faster.
- RPC program numbers map is in /etc/rpc if the names look cryptic.
```