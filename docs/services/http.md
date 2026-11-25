# HTTP/HTTPS

_Port: 80, 443_

## Service Enumeration

Basic interaction
```bash
curl -i http://$ip/
```
Other curl
```bash
-k      # ignore self-signed certs
-I      # save headers only
-X      # Request option
```
Fingerprint the web tech stack (framework, server, version)
```bash
whatweb http://$ip/
```

nikto scan
```bash
nikto -host=http://$ip -maxtime=30m -o $outfile
```

wpscan
```bash
wpscan --url http://$ip -U 'username1,username2' --enumerate u
```

use ffuf recursively
```bash
ffuf -c -w \\
/usr/share/seclists/Discovery/Web-Content/raft-large-directories-lowercase.txt \\
-u http://$ip/FUZZ -recursion -recursion-depth 2
```