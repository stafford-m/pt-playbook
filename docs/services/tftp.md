# TFTP

_Port: 69_

## Service Usage

Open interactive tftp session
```bash
tftp $ip
```
Download a file (non-interactive)
```bash
tftp $ip -c get file
```
Upload a file (non-interactive)
```bash
tftp $ip -c put file
```

## Config Paths

tftpd
```text
/etc/default/tftpd-hpa
/etc/xinetd.d/tftp
```

Config quick-reference:
```text
TFTP_DIRECTORY -> /srv/tftp or /tftpboot (what's served)
```

### Common gotchas
```text
- UDP-based (69) and tiny blocks — use -c carefully; large transfers fail.
- No directory listing — you must know/guess filenames. Use wordlists of common device config names.
```