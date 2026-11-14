# Information Exposure

## User inspection

__Search for leaked credentials__

Environment
```bash
env
cat .bashrc # dotfiles (.bashrc/.zshrc, .bash_history/.zsh_history)
```

Pushing further on a leaked password
```bash
crunch 6 6 -t Lab%%% > wordlist

hydra -l eve -P wordlist  192.168.50.214 -t 4 ssh -V
```

## Service Footprints

__Search for leaked credentials__

Inspect running services
```bash
watch -n 1 "ps -aux | grep pass"
```

Inspecting loopback traffic
```bash
sudo tcpdump -i lo -A | grep "pass"
```