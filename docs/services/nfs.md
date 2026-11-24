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