# RPC

_Port: 111_

## Service Usage

Query registered RPC services
```bash
rpcinfo -p $ip
```
Query a specific program (filter for nfs)
```bash
rpcinfo -p $ip | grep nfs
```

Query SMB/MSRPC via rpcclient (if applicable)
```bash
rpcclient -U "" -N $ip -c "srvinfo"
```

## Config Paths
rpcbind
```text
/etc/rpc        # maps RPC program numbers to names
/etc/inetd.conf # which RPC services are enabled
```

### Common gotchas
```text
- 111 alone is useless; its whole value is pointing you to NFS/mountd/other RPC services.
- rpcinfo vs nmap --script rpcinfo — both fine, rpcinfo is faster.
- RPC program numbers map is in /etc/rpc if the names look cryptic.
```