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

DCOM execution (if WMI blocked)
```bash
impacket-dcomexec $domain/$un:$pw@$ip
```