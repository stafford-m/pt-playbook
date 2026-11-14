# Exposed Credentials

## The Basics

Search C drive by file type
```powershelll
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
```

Searching for important Apache Files
```powershell
Get-ChildItem -Path C:\xampp -Include *.txt,*.ini -File -Recurse -ErrorAction SilentlyContinue
```

Searching for generic interesting files
```powershell
Get-ChildItem -Path C:\Users\dave\ -Include *.txt,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue
```

Get Environmental Variables
```powershell
set
Get-ChildItem env:
```

Open cmd as another user
```powershell
runas /user:backupadmin cmd
```


## Exposed Powershell Information

PS History
```powershell
Get-History
```

History File Location
```powershell
(Get-PSReadlineOption).HistorySavePath
```

Powershell transcript location
```powershell
ls C:\Users\Public\Transcripts\
```

PS Session options
```powershell
$password = ConvertTo-SecureString "password123" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential("Administrator", $password)
Enter-PSSession -ComputerName $computer -Credential $cred
```

evil-winrm
```powershell
evil-winrm -i $ip -u Administrator -p $password
```