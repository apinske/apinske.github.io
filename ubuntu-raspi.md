# Ubuntu on Raspberry Pi

* `curl https://cdimage.ubuntu.com/releases/22.04/release/ubuntu-22.04-preinstalled-server-arm64+raspi.img.xz | xz -d - > sd.img`
* `diskutil unmountDisk /dev/disk2`
* `sudo dd bs=1m if=sd.img of=/dev/rdisk2`
* config WiFi in `/Volumes/system-boot/network-config`
* `diskutil eject /dev/disk2`
* boot, ssh
* update: `sudo apt update && sudo apt upgrade`

## .bashrc
```bash
alias env_sshauth='export SSH_AUTH_SOCK="$(tmux show-env | sed -n 's/^SSH_AUTH_SOCK=//p')"'
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
