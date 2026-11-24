# WinRM

### Port: 5985, 5986

## Service Usage

Connect to WinRM with plaintext credentials
```bash
evil-winrm -i $ip -u $un -p $pw
```
Connect using pass-the-hash (NTLM hash)
```bash
evil-winrm -i $ip -u $un -H $ntlm_hash
```
Connect over TLS (5986)
```bash
evil-winrm -i $ip -u $un -p $pw -S
```