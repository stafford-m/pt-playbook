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

## 3. Password Spraying / Bruteforcing

N/A for NFS itself — no password auth. Skip this section.

## Config Paths

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