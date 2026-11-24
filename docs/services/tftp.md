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