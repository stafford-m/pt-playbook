# Linux Enumeration

Basic information
```bash
id
cat /etc/passwd
hostname
env
sudo -l
```

System information
```bash
cat /etc/issue
cat /etc/os-release
uname -a
```

Processes
```bash
ps aux
ls -lah /etc/cron* # .daily, .hourly, .monthly, .weekly, .d
crontab -l
sudo crontab -l
grep “CRON” /var/log/syslog # cron log file
```

Network
```bash
ip a
ifconfig -a
route or routel
ss -anp
netstat
```

Firewall rules
```bash
iptables # with permissions
/etc/iptables # iptables-persistent special, permissions
cat /iptables/rules.v4
```

Software and Kernel Modules
```bash
dpkg -l
lsmod # enumerate loaded kernel modules
/sbin/libata <kernel mod name> (requires full path)
```

filesystem
```bash
cat /etc/fstab # drives mounted at boot
mount # list all mounted filesystems
lsblk # list available drives

find / -perm -u=s -type f 2>/dev/null
find / -perm -g=s -type f 2>/dev/null (generally not as risky, since inheriting group)

find / -writable -type d 2>/dev/null # Writable directories

dotfiles (.bashrc/.zshrc, .bash_history/.zsh_history
```