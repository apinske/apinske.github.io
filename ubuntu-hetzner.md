# Ubuntu on Hetzner Server
## Rescue Installation
* `installimage -n srv1.pinske.dev -r yes -l 1 -d sda,sdb -p /boot:ext2:512M,/:ext4:all -i /root/images/Ubuntu-2404-noble-amd64-base.tar.gz -t yes -a`

## Setup
* update: `apt update && apt upgrade`
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
* `for i in $(seq 1 4); do sudo ip tuntap add dev tap$i mode tap user apinske && sudo ip link set tap$i master br0 && sudo ip link set tap$i up; done`

## Ubuntu VM
```sh
#!/bin/sh
if [ ! -f vda.img ]; then
  wget -O- https://cloud-images.ubuntu.com/noble/current/noble-server-cloudimg-amd64.tar.gz | tar xzf - noble-server-cloudimg-amd64.img
  wget -O initrd https://cloud-images.ubuntu.com/noble/current/unpacked/noble-server-cloudimg-amd64-initrd-generic
  wget -O vmlinuz https://cloud-images.ubuntu.com/noble/current/unpacked/noble-server-cloudimg-amd64-vmlinuz-generic

  VM_NAME=$(basename $PWD)
  VM_NO=$(echo $VM_NAME | tr -cd '[:digit:]')
  mkdir ci
  cat << EOF > ci/user-data
#cloud-config
password: ubuntu
chpasswd:
  expire: False
ssh_pwauth: False
ssh_authorized_keys:
  - $(cat ~/.ssh/authorized_keys)
EOF
  cat << EOF > ci/meta-data
instance-id: iid-local-$VM_NAME
local-hostname: $VM_NAME
EOF
  cat << EOF > ci/network-config
version: 2
ethernets:
  ens4:
    addresses:
      - 2a01:4f8:171:334c::a$VM_NO/64
    gateway6: fe80::1
    nameservers:
      addresses:
          - 2a01:4f8:0:1::add:9898
          - 2a01:4f8:0:1::add:9999
          - 2a01:4f8:0:1::add:1010
EOF
  genisoimage -output ci.iso -volid cidata -joliet -rock ci/
  rm -rf ci

  mv noble-server-cloudimg-amd64.img vda.img
  truncate -s 10G vda.img

  MACADDR="52:54:00:$(dd if=/dev/urandom bs=512 count=1 2>/dev/null | md5sum | sed 's/^\(..\)\(..\)\(..\).*$/\1:\2:\3/')"
  cat << EOF > run.sh
#!/bin/sh
../cloud-hypervisor-static --api-socket path=api \\
    --cpus boot=2 --memory size=16G \\
    --net tap=tap$VM_NO,mac=$MACADDR \\
    --disk path=vda.img path=ci.iso,readonly=on \\
    --kernel vmlinuz --initramfs initrd --cmdline "console=hvc0 root=/dev/vda"
EOF
  chmod +x run.sh
fi
```

## HAProxy
* `sudo apt install haproxy`
* `sudo vi /etc/haproxy/haproxy.cfg`
* `sudo systemctl restart haproxy`

```
frontend http_redirect
        bind *:80
        mode http
        option httplog
        http-request redirect scheme https unless { ssl_fc }
frontend https_proxy
        bind *:443
        mode tcp
        option tcplog
        tcp-request inspect-delay 5s
        tcp-request content capture req.ssl_sni len 25
        use_backend %[req.ssl_sni,lower]

backend one.pinske.it
        mode tcp
        server one1 [2a01:4f8:171:334c::a1]:443
```

## Caddy (VM)
* `sudo apt install caddy`
* `sudo vi /etc/caddy/Caddyfile`
* `sudo systemctl reload caddy`

```
one.pinske.it {
    reverse_proxy localhost:8080
}
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
