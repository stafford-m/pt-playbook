# Scanning & Banner Grabbing

## netcat
basic nc banner grab
```bash
nc $ip $port
```

```bash
-n      # no resolve dns
-v      # verbose
-vv     # very verbose
-w 5    # timeout 5
-z      # zero i/o - don't send data
-l      # listen
-p      # specify port (for listen)
```

## nmap
gettings all help for a specific protocol
```bash
nmap --script-help smtp-*
```

search for nmap scripts
```bash
ls /usr/share/nmap/scripts/ | grep smtp-*
```

run default scripts on port
```bash
nmap -sC -sV -p25 $ip
```

enumerate all tcp ports & os detection
```bash
nmap -p- -O $ip
```

enumerate top 200 udp
```bash
sudo nmap -sU --open --top-ports=200 $ip
```

## curl
curl can be used for protocols other than http
```bash
curl -v ftp://$ip:21/ 2>&1 | head -n 5
curl -s --max-time 8 ftp://anonymous:anonymous@$ip/
```