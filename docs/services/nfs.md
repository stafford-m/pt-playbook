# NFS

_Port: 2049_

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

### Common gotchas
-```text
- "no_root_squash" = your root maps to target root (write setuid binaries).
- Mount fails? Use -o nolock, and make sure rpcbind (111) is reachable too.
- Read-only exports still leak creds/configs — don't skip them.
- NFS over UDP may need -o udp on old targets.
```