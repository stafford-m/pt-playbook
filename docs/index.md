# Service Enumeration

```bash
nmap -sC -sV -p- $ip

sudo nmap -sU --open --top-ports=200 192.168.110.8

ls /usr/share/nmap/scripts | grep smtp-*

nmap --script-help smtp-*

nc -nvv -w 1 -z 192.168.50.152 3388-3390
```