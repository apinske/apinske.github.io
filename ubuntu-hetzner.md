# Ubuntu on Hetzner Server
## Rescue Installation
* `installimage -n srv1.pinske.dev -r yes -l 1 -d sda,sdb -p /boot:ext2:512M,/:ext4:all -i /root/images/Ubuntu-2010-groovy-64-minimal.tar.gz -t yes -a`

## Setup
* update: `apt update && apt upgrade`
* `apt install tmux`
* `adduser apinske --disabled-password --gecos "" --ingroup users`
* `adduser apinske kvm`
* `cp -R /root/.ssh /home/apinske/`
* `chown -R apinske:users /home/apinske/.ssh`
* `echo "apinske ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/apinske`
* `cp /run/systemd/resolve/resolv.conf /root/resolv.conf` (Backup)

## KVM
* `sudo apt install qemu-system-x86`
* ``

## CRC
* `wget https://mirror.openshift.com/pub/openshift-v4/clients/crc/latest/crc-linux-amd64.tar.xz`
* `mkdir -p .local/bin && tar --strip-components=1 -C .local/bin -xf crc-linux-amd64.tar.xz '*crc'`
* `crc config set memory 32768`
* `crc config set disk-size 256`
* `crc setup`
* `sudo sh -c 'cat /root/resolv.conf >> /etc/resolv.conf'`
* `crc start`
* `ssh -i ~/.crc/machines/crc/id_ecdsa core@$(crc ip)`

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

## /etc/netplan/02-kvm.yaml
```yaml
network:
    bridges:
        br0:
            macaddress: 02:00:00:00:00:01
            interfaces: [enp0s31f6]
            dhcp4: true
    version: 2
```
