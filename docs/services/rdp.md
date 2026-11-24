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