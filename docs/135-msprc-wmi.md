# 135 - MSRPC / WMI (DCOM)

## Service Usage

Query MSRPC endpoints
```bash
impacket-rpcdump $ip
rpcclient -U "" -N $ip -c "srvinfo"
```

WMI exec (needs creds)
```bash
impacket-wmiexec $domain/$un:$pw@$ip
```

## 0. Fast Path (try these first)

Endpoint dump (reveals what RPC services are exposed):
```bash
impacket-rpcdump $ip
```
Usually 135 pairs with SMB — so most of your 135 work is really SMB/WMI exec. See SMB notes.

## 1. Banner Grab & Unauthenticated Enumeration

nmap
```bash
nmap -p 135 -sV -sC $ip
```

Enumerate endpoints/auth
```bash
impacket-rpcdump $ip
rpcclient -U "" -N $ip -c "enumdomusers"
```

## 2. Authenticated Enumeration

With creds
```bash
rpcclient -U "$un%$pw" $ip -c "enumdomusers; enumdomgroups; netshareenumall"
impacket-wmiexec $domain/$un:$pw@$ip       # shell via WMI
```

WMI query
```bash
impacket-wmiexec $domain/$un:$pw@$ip 'systeminfo'
```

## 3. Password Spraying / Bruteforcing

*Same as SMB — MSRPC is just another transport for the same creds.*

```bash
rpcclient -U "$un%$pw" $ip -c "srvinfo"    # single check
```

## 5. Quick Wins

### 5.1 Valid creds → WMI shell
```bash
impacket-wmiexec $domain/$un:$pw@$ip
```

### 5.2 DCOM execution (if WMI blocked)
```bash
impacket-dcomexec $domain/$un:$pw@$ip
```

### 5.3 Anonymous endpoint discovery
```bash
impacket-rpcdump $ip
```

## 6. Config Paths

Windows DCOM
```text
HKLM\SOFTWARE\Microsoft\OLE          # DCOM settings
```

## 7. Post-Exploit / Pivot

- WMI shell → PrivEsc (see Windows PrivEsc notes)
- Endpoint dump → find additional attack surface

### Common gotchas
```text
- 135 open usually means 445/139 open too — treat them together.
- wmiexec is quieter than psexec; try wmiexec first for a shell.
- "Access denied" on rpcclient = need creds (most boxes disable anonymous SID enum).
```