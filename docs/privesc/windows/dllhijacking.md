# DLL Hijacking

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