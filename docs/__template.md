# [PORT] - [PROTOCOL NAME]

## 1. Banner Grab & Manual Verification

Basic Socket Banner Grab
```bash
nc -nv $IP $PORT
```
If SSL/TLS is enabled on this port
```bash
openssl s_client -connect $IP:$PORT -crlf
```
Protocol-specific quick probe (e.g., curl, telnet)
```bash
curl -ik http://$IP:$PORT/
```

## 2. Unauthenticated Enumeration

## 3. Authenticated Enumeration

## 4. Password Spraying / Bruteforcing

## 5. Attack Vectors & Shell Primatives

## 6. Quick Wins

## 7. Config Paths
