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
* `/etc/sysctl.conf`: `sysctl -w net.ipv6.conf.all.forwarding=1`

## KVM
* edit netplan
* `sudo netplan try`
* `sudo apt install qemu-system-x86 genisoimage`
* `sudo ip tuntap add dev tap0 mode tap user apinske`
* `sudo ip link set tap0 master br0`
* `sudo ip link set tap0 up`

## Ubuntu VM
*  `wget -O- https://cloud-images.ubuntu.com/hirsute/current/hirsute-server-cloudimg-amd64.tar.gz | tar xzf - hirsute-server-cloudimg-amd64.img`
*  `wget -O initrd https://cloud-images.ubuntu.com/hirsute/current/unpacked/hirsute-server-cloudimg-amd64-initrd-generic`
*  `wget -O vmlinuz https://cloud-images.ubuntu.com/hirsute/current/unpacked/hirsute-server-cloudimg-amd64-vmlinuz-generic`
*  `cp hirsute-server-cloudimg-amd64.img hd.raw`
*  `dd if=/dev/zero bs=1M count=9000 >> hd.raw`
*  cidata
    * user-data, meta-data, network-config
    * `genisoimage -output cidata.iso -volid cidata -joliet -rock cidata/`
* `MACADDR="52:54:00:$(dd if=/dev/urandom bs=512 count=1 2>/dev/null | md5sum | sed 's/^\(..\)\(..\)\(..\).*$/\1:\2:\3/')"; echo $MACADDR`
*  run
    ```sh
    qemu-system-x86_64 \
        -nodefaults -nographic \
        -machine ubuntu -cpu host -accel kvm -smp 2 -m 16G \
        -chardev stdio,id=screen,mux=on,signal=off -serial chardev:screen -mon screen \
        -netdev tap,id=net1,ifname=tap0,script=no,downscript=no -device virtio-net,netdev=net1,mac=<MACADDR> \
        -netdev user,id=net2,ipv4=on -device virtio-net,netdev=net2,mac=02:00:00:00:00:a1 \
        -blockdev driver=file,node-name=hd,filename=hd.raw -device virtio-blk,drive=hd \
        -blockdev driver=file,node-name=cd,filename=cidata.iso -device virtio-blk,drive=cd \
        -kernel vmlinuz -initrd initrd -append "console=ttyS0 root=/dev/vda"
    ```

## Alpine VM
* `wget https://dl-cdn.alpinelinux.org/alpine/v3.14/releases/x86_64/alpine-virt-3.14.2-x86_64.iso`
* ...
*  run
    ```sh
    qemu-system-x86_64 \
        -nodefaults -nographic \
        -machine ubuntu -cpu host -accel kvm -smp 2 -m 8G \
        -blockdev driver=file,node-name=cd,filename=alpine-virt-3.14.2-x86_64.iso,read-only=on,force-share=on -device virtio-blk,drive=cd \
        -chardev stdio,id=screen,mux=on,signal=off -serial chardev:screen -mon screen \
        -netdev tap,id=net,ifname=tap1,script=no,downscript=no -device virtio-net,netdev=net \
        -blockdev driver=file,node-name=hd,filename=hd.raw -device virtio-blk,drive=hd
    ```

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

## /etc/netplan/01-netcfg.yaml
```diff
    ethernets:
      enp0s31f6: 
+       dhcp4: false
+       dhcp6: false
+   bridges:
+     br0:
+       interfaces: [enp0s31f6]
        addresses:
```

## ci-data/meta-data
```
instance-id: iid-local01
local-hostname: ubuntu1
```

### ci-data/user-data
```
#cloud-config
password: ...
chpasswd: { expire: False }
ssh_pwauth: False
ssh_authorized_keys:
  - ssh-rsa ...
```

## ci-data/network-config
```yaml
version: 2
ethernets:
  ens2:
    addresses:
      - 2a01:4f8:171:334c::a1/64
    gateway6: fe80::1
    nameservers:
      addresses:
          - 2a01:4f8:0:1::add:9898
          - 2a01:4f8:0:1::add:9999
          - 2a01:4f8:0:1::add:1010
  ens3:
    dhcp4: true
    link-local: []
```
