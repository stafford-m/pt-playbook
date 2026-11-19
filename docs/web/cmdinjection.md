# Command Injection

An example
```bash
curl -X POST --data 'Archive=git%3Bipconfig' http://192.168.50.189:8000/archive
```

I can't show it here, but there is a lot of payloads you could throw into a command injection vulnerability and I'll just have to update the resources later to show this.
You get this idea though, you're running commands on the OS, if you can get a PoC on that you're golden.