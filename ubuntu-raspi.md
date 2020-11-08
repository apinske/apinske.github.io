# Ubuntu on Raspberry Pi

* `brew cask install raspberry-pi-imager`
* Ubuntu 20.04.1 LTS (64bit)
* wait for auto-update to complete: `tail -f /var/log/unattended-upgrades/unattended-upgrades-dpkg.log`
* update: `sudo apt update && sudo apt upgrade`

## .bashrc
```bash
if command -v tmux &> /dev/null && [ -z "$TMUX" ]; then
  tmux attach -t default || tmux new -s default
fi
```

## .tmux.conf
```
set -g prefix C-a
bind C-a send-prefix
unbind C-b
```
