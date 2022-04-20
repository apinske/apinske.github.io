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
* `ebtables -t nat -A POSTROUTING -j snat --to-src <MACADDR>`

## KVM
* edit netplan
* `sudo netplan try`
* `sudo apt install qemu-system-x86 genisoimage`
* `sudo ip tuntap add dev tap0 mode tap user apinske`
* `sudo ip link set tap0 master br0`
* `sudo ip link set tap0 up`

## Ubuntu VM
```sh
#!/bin/sh                           
if [ ! -f vda.img ]; then
  wget -O- https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.tar.gz | tar xzf - jammy-server-cloudimg-amd64.img
  wget -O initrd https://cloud-images.ubuntu.com/jammy/current/unpacked/jammy-server-cloudimg-amd64-initrd-generic
  wget -O vmlinuz https://cloud-images.ubuntu.com/jammy/current/unpacked/jammy-server-cloudimg-amd64-vmlinuz-generic
                                                   
  VM_NAME=$(basename $PWD)
  VM_NO=$(echo $VM_NAME | tr -cd '[:digit:]')
  mkdir cidata  
  cat << EOF > cidata/user-data   
#cloud-config                     
password: ubuntu                  
chpasswd:
  expire: False
ssh_pwauth: False 
ssh_authorized_keys:
  - $(cat ~/.ssh/authorized_keys)                                                                     
EOF            
  cat << EOF > cidata/meta-data
instance-id: iid-local-$VM_NAME             
local-hostname: $VM_NAME 
EOF
  cat << EOF > cidata/network-config                                                                                                                                                                        
version: 2           
ethernets:
  ens2:              
    addresses:               
      - 2a01:4f8:171:334c::a$VM_NO/64                                                                 
    gateway6: fe80::1                                                                                 
    nameservers:                                                                                                                                                                                            
      addresses:                                                                                      
          - 2a01:4f8:0:1::add:9898                                                                    
          - 2a01:4f8:0:1::add:9999                                                                    
          - 2a01:4f8:0:1::add:1010                                                                    
  ens3:
    dhcp4: true
    link-local: []
EOF
  genisoimage -output cidata.iso -volid cidata -joliet -rock cidata/
  rm -rf cidata

  mv jammy-server-cloudimg-amd64.img vda.img
  truncate -s 10G vda.img

  MACADDR="52:54:00:$(dd if=/dev/urandom bs=512 count=1 2>/dev/null | md5sum | sed 's/^\(..\)\(..\)\(..\).*$/\1:\2:\3/')"
  cat << EOF > run.sh
#!/bin/sh
qemu-system-x86_64 \\
    -nodefaults -nographic \\
    -machine ubuntu -cpu host -accel kvm -smp 2 -m 16G \\
    -chardev stdio,id=screen,mux=on,signal=off -serial chardev:screen -mon screen \\
    -netdev tap,id=net1,ifname=tap$((VM_NO-1)),script=no,downscript=no -device virtio-net,netdev=net1,mac=$MACADDR \\
    -netdev user,id=net2,ipv4=on -device virtio-net,netdev=net2,mac=02:00:00:00:00:f$VM_NO \\
    -blockdev driver=file,node-name=hd,filename=vda.img -device virtio-blk,drive=hd \\
    -blockdev driver=file,node-name=cd,filename=cidata.iso -device virtio-blk,drive=cd \\
    -kernel vmlinuz -initrd initrd -append "console=ttyS0 root=/dev/vda"
EOF
  chmod +x run.sh
fi
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
