# KVM on ARM64 (Raspberry Pi 4)

* [install Ubuntu](ubuntu-raspi.md)
* install QEMU
    * `sudo apt install qemu-system-arm qemu-efi-aarch64`
    * `sudo adduser ubuntu kvm`
    * re-login
* setup bridge network
    * edit netplan
    * reboot
    * `sudo ip tuntap add dev tap0 mode tap user ubuntu`
    * `sudo ip link set tap0 master br0`
* install Alpine guest
    * `wget http://dl-cdn.alpinelinux.org/alpine/v3.12/releases/aarch64/alpine-virt-3.12.1-aarch64.iso`
    * `qemu-img create hd.raw 10G`
    * `qemu-system-aarch64 -nodefaults -nographic -machine virt -cpu host -accel kvm -smp 2 -m 2G -bios /usr/share/qemu-efi-aarch64/QEMU_EFI.fd -blockdev driver=file,node-name=cd,filename=alpine-virt-3.12.1-aarch64.iso -device virtio-blk-device,drive=cd -chardev stdio,id=screen,mux=on,signal=off -serial chardev:screen -monitor chardev:screen -netdev tap,id=net,ifname=tap0,script=no,downscript=no -device virtio-net-device,netdev=net -blockdev driver=file,node-name=hd,filename=hd.raw -device virtio-blk-device,drive=hd`
* setup Alpine
    * `echo -en 'n\np\n1\n\n+512M\nn\np\n2\n\n\nw\n' | fdisk /dev/vda`
    * `mkfs.vfat /dev/vda1`
    * `echo "/dev/vda1 /media/vda1 vfat rw 0 0" >> /etc/fstab`
    * `mkdir /media/vda1`
    * `mount -a`
    * `setup-alpine -e`
    * add ssh-key `mkdir ~/.ssh && echo '...' > ~/.ssh/autorized_keys`
* *optional:* setup docker
    * uncomment community repo in `/etc/apk/repositories`
    * `apk update`
    * `apk add e2fsprogs`
    * `mkfs.ext4 /dev/vda2`
    * `echo "/dev/vda2 /var/lib/docker ext4 rw 0 0" >> /etc/fstab`
    * `mkdir /var/lib/docker`
    * `mount -a`
    * `apk add docker`
    * `rc-update add docker boot`
    * `service docker start`
    * test: `docker run --rm -it alpine`
* *optional:* setup k3d
    * `wget -O /usr/local/bin/k3d https://github.com/rancher/k3d/releases/download/v3.2.0/k3d-linux-arm64`
    * `chmod +x /usr/local/bin/k3d`
    * *good idea?* `lbu add /usr/local/bin`
    * `k3d cluster create raspi -a 1 -p 80:80@loadbalancer`
    * remote `ssh root@alpine '/usr/local/bin/k3d kubeconfig get -a | sed -e "s/0.0.0.0/$(hostname)/"'`
    * test: `kubectl --context k3d-raspi run -it alpine --image=alpine --rm`
* save config
    * `lbu commit`
* [QEMU docs](https://www.qemu.org/docs/master/system/invocation.html)

## /etc/netplan/50-cloud-init.yaml
```yaml
network:
    ethernets:
        eth0:
            dhcp4: false
            match:
                driver: bcmgenet smsc95xx lan78xx
            optional: true
            set-name: eth0
    bridges:
        br0:
            interfaces: [eth0]
            dhcp4: true
    version: 2
```
