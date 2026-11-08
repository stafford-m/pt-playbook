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
enum4linux -a $ip           # does most of the below in one shot
```

## Config Paths

Samba
```text
/etc/samba/smb.conf
/usr/local/samba/lib/smb.conf
```
Windows Registry (share perms / audit)
```text
HKLM\SYSTEM\CurrentControlSet\Services\LanmanServer\Shares
```

Config quick-reference:
```text
smb.conf     -> check for: writable = yes, guest ok = yes, valid users, restrict anonymous
Windows      -> check ADMIN$ / C$ write rights (that's the win, not the config file)
```

### Common gotchas
```text
- 445 closed but 139 open? Try SMB1 via -p 139 / add 'client max protocol = SMB2' to smb.conf if modern target refuses SMB1.
- "NT_STATUS_LOGON_FAILURE" on a correct-looking pw? Try WORKGROUP vs domain, or add -d . for local auth.
- SMB signing disabled/required: 'nmap --script smb2-security-mode' reveals it — relay attacks need signing disabled.
- Kerberos vs NTLM: if clock skew / "KRB_AP_ERR_SKEW", check target timezone/drift.
```

### Command map (moved psexec/wmiexec here for context)
```text
impacket-psexec  -> interactive SYSTEM shell (needs admin, port 445)
impacket-wmiexec -> shell over WMI (port 135/5985, quieter than psexec)
impacket-smbexec -> semi-interactive, uses SMB only
netexec          -> enumeration + creds + --exec-method smbexec/atexec for command exec
```