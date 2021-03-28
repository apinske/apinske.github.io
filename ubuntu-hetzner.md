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

## KVM
* edit netplan
* `sudo netplan try`
* `sudo apt install qemu-system-x86 genisoimage`
* `sudo ip tuntap add dev tap0 mode tap user apinske`
* `sudo ip link set tap0 master br0`
* `sudo ip link set tap0 up`

## Alpine
* `wget https://dl-cdn.alpinelinux.org/alpine/v3.13/releases/x86_64/alpine-virt-3.13.3-x86_64.iso`
* `qemu-img create hd.raw 10G`
* run
    ```sh
    qemu-system-x86_64 \
        -nodefaults -nographic \
        -machine ubuntu -cpu host -accel kvm -smp 2 -m 16G \
        -blockdev driver=file,node-name=cd,filename=alpine-virt-3.13.3-x86_64.iso,read-only=on,force-share=on -device virtio-blk,drive=cd \
        -chardev stdio,id=screen,mux=on,signal=off -serial chardev:screen -mon screen \
        -netdev tap,id=net,ifname=tap0,script=no,downscript=no -device virtio-net,netdev=net \
        -blockdev driver=file,node-name=hd,filename=hd.raw -device virtio-blk,drive=hd
    ```

## Ubuntu
*  `wget -O- https://cloud-images.ubuntu.com/groovy/current/groovy-server-cloudimg-amd64.tar.gz | tar xzf - groovy-server-cloudimg-amd64.img`
*  `wget -O initrd https://cloud-images.ubuntu.com/groovy/current/unpacked/groovy-server-cloudimg-amd64-initrd-generic`
*  `wget -O vmlinuz https://cloud-images.ubuntu.com/groovy/current/unpacked/groovy-server-cloudimg-amd64-vmlinuz-generic`
*  `cp groovy-server-cloudimg-amd64.img hd.raw`
*  run
    ```sh
    qemu-system-x86_64 \
        -nodefaults -nographic \
        -machine ubuntu -cpu host -accel kvm -smp 2 -m 16G \
        -chardev stdio,id=screen,mux=on,signal=off -serial chardev:screen -mon screen \
        -netdev tap,id=net,ifname=tap0,script=no,downscript=no -device virtio-net,netdev=net \
        -blockdev driver=file,node-name=hd,filename=hd.raw -device virtio-blk,drive=hd \
        -kernel vmlinuz -initrd initrd -append "console=ttyS0 root=/dev/vda"
    ```

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

## /etc/network/interfaces
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet6 static
        address <IPv6>/64
        gateway fe80::1
```
