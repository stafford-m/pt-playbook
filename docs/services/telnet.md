# Telnet

_Port: 23_

## Service Usage

Connect with telnet
```bash
telnet $ip
```
Connect with nc
```bash
nc -nv $ip 23
```

## Config Paths

telnetd (Linux, if xinetd/inetd)
```text
/etc/inetd.conf
/etc/xinetd.d/telnet
```

### Common gotchas
```text
- Telnet = plaintext; creds sniffable if you're MITM (rare on OSCP).
- Network devices (routers/switches) are the main telnet targets on exams.
- No encryption — don't expect SSH-like behaviour.
```