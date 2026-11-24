# SMB

### Port: 139, 445

## Service Usage
Attempt null/anonymous session and list shares
```bash
smbclient -L $ip -N
```

Null/anonymous share connecting
```bash
smbclient //$ip/$share -N
```

Authenticated share listing
```bash
smbclient -L $ip -U $un%$pw
```

Authenticated share connecting
```bash
smbclient //$ip/$share -U $un%$pw
```

nxc enumeration
```bash
netexec smb $ip -u '' -p '' --shares
```

addtional nxc scripts
```bash
--shares
--users
--groups
--sessions
--local-groups
--loggedon-users
```

mount a share
```bash
mount -t cifs //$ip/$share /mnt/smb -o username=$un,password=$pw
```

Common Commands
```text
ls
cd $dir
get $file           # download single file
mget *              # download all in dir (toggle prompt off with 'prompt')
put $file           # upload single file
mput *              # multiple upload
recurse ON          # enable recursive for mget/mput
prompt OFF          # disable per-file confirmation
```

## Enumeration
SMB Sweep
```bash
nmap -v -p 139,445 -oG smb.txt 192.168.50.1-254
```

Queries valid netbios names
```bash
sudo nbtscan -r 192.168.50.0/24
```

attempts OS discovery
__some nmap scripts check for exploitable versions__
```bash
nmap -v -p 139,445 --script smb-os-discovery 192.168.50.152
```

windows tools
```bash
net view \\dc01 /all
```

Enumerate without creds on enum4linux
```bash
enum4linux -a $ip           # does most of the enum
```