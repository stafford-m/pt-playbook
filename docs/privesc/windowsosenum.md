# Windows Enumeration

User info
```powershell
whoami
whoami /groups
whoami /priv # sudo -l

net user
Get-LocalUser
```

Group info
```powershell
net localgroup $group
Get-LocalGroup
Get-LocalGroupMember $group
```

Network info
```powershell
ipconfig /all
route print
netstat -ano
```

Software and update info
```powershell
systeminfo
Get-Process
Get-CimInstance -Class win32_quickfixengineering | Where-Object { $_.Description -eq "Security Update" }
Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
reg query HKLM\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall /s /v DisplayName
```

Powershell info
```powershell
Get-History
(Get-PSReadlineOption).HistorySavePath
Get-ChildItem C:\Users\Public\Transcripts\
type C:\Users\dave\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

Smb and files
```powershell
net view \\dc01 /all
Get-ChildItem -Path C:\Users\dave\ -Include *.txt,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue
Get-ChildItem -Path C:\xampp -Include *.txt,*.ini -File -Recurse -ErrorAction SilentlyContinue
Get-ChildItem -Path C:\ -Include *.kdbx, *.txt, *.docx -File -Recurse -ErrorAction SilentlyContinue

Get-Content
type
```

Login as batch job
```powershell
runas /user:backupadmin cmd
```

PSSession
```powershell
$password = ConvertTo-SecureString $pass -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential("daveadmin", $password)
Enter-PSSession -ComputerName $computername -Credential $cred
```

remote connection
```powershell
evil-winrm -i 192.168.0.25 -u $user -p $pass