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

## 6. Config Paths

Apache
```text
/etc/apache2/apache2.conf
/etc/apache2/sites-available/000-default.conf
/var/www/html/
```
Nginx
```text
/etc/nginx/nginx.conf
/etc/nginx/sites-available/default
/usr/share/nginx/html/
```
Tomcat
```text
/opt/tomcat/conf/tomcat-users.xml
/opt/tomcat/webapps/
```
WordPress
```text
/var/www/html/wp-config.php
/var/www/html/wp-content/
```

Config quick-reference:
```text
wp-config.php   -> DB creds in plaintext
tomcat-users.xml -> manager/role creds, weak defaults
docker-compose.yml / .env -> service creds
```

### Common gotchas
```text
- Self-signed cert? curl -k or set insecure. Browsers block — use curl/burp.
- 403 on dir brute? Check for virtual host (Host header) or add -mc 403 to see them.
- .php .bak .old .txt .sql — always add extension list; source disclosure is huge.
- Upload dir non-executable? Look for .phtml/.php5 or bypass content-type/magic bytes.
- rate limiting? Slow hydra with -t 2 and add delay. Spray (one pass, many users) > brute (many passes, one user).
```