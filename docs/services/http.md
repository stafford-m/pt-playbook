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