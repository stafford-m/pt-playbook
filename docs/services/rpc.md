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

un/pw sign in
```bash
rpcclient -U "$domain/$username%$password" $ip
```

common rpc commands, non-exhaustive
```bash
srvinfo
querydominfo
getdompwinfo
enumdomusers
queryuser <username|rid>
querydomusergroups <username|rid>
```