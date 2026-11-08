# RDP

_Port: 3389_

## Service Usage

Connect with clipboard sharing
```bash
xfreerdp /v:$ip /u:$un /p:$pw +clipboard
```

```bash
/v:$ip
/u:$un
/p:$pw
/cert:ignore
/dynamic-resolution
+clipboard
+drives:/tmp,/share
```

## Config Paths

RDP settings
```text
HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\
HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\
```

Config quick-reference:
```text
fDenyTSConnections -> 0 = RDP enabled
SecurityLayer -> RDP vs SSL
UserAuthentication -> NLA on/off
```

### Common gotchas
```text
- NLA = need valid creds before you see a login prompt. No creds? Enum is limited.
- CredSSP / NLA errors with xfreerdp? Try /sec:nla or /sec:rdp to adjust.
- Clipboard not working? Reconnect with +clipboard.
- RDP slow for brute — never brute RDP first; validate via SMB/WinRM then log in once.
```