# Ubuntu on Raspberry Pi

* `wget https://cdimage.ubuntu.com/releases/20.10/release/ubuntu-20.10-preinstalled-server-arm64+raspi.img.xz`
* `xz -d ubuntu-20.10-preinstalled-server-arm64+raspi.img.xz`
* `diskutil unmountDisk /dev/disk3`
* `sudo dd bs=1m if=ubuntu-20.10-preinstalled-server-arm64+raspi.img of=/dev/rdisk3`
* `diskutil eject /dev/disk3``
* boot, ssh
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
