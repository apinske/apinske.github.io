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
    * `qemu-img create hd1.raw 1G`
    * `qemu-img create hd2.raw 1G`
    * `qemu-system-aarch64 -nodefaults -nographic -machine virt -cpu host -accel kvm -smp 2 -m 2G -bios /usr/share/qemu-efi-aarch64/QEMU_EFI.fd -blockdev driver=file,node-name=cd,filename=alpine-virt-3.12.1-aarch64.iso -device virtio-blk-device,drive=cd -chardev stdio,id=screen,mux=on,signal=off -serial chardev:screen -monitor chardev:screen -netdev tap,id=net,ifname=tap0,script=no,downscript=no -device virtio-net-device,netdev=net -blockdev driver=file,node-name=hd1,filename=hd1.raw -device virtio-blk-device,drive=hd1 -blockdev driver=file,node-name=hd2,filename=hd2.raw -device virtio-blk-device,drive=hd2`
* setup Alpine
    * `mkfs.vfat /dev/vda`
    * `echo "/dev/vda /media/vda vfat rw 0 0" >> /etc/fstab`
    * `mkdir /media/vda`
    * `mount -a`
    * `setup-alpine`
    * uncomment community repo in `/etc/apk/repositories`
    * `apk update`
    * `apk add docker`
    * `rc-update add docker boot`
    * `service docker start`
    * `lbu add /var/lib/docker`
    * `lbu commit`
* [QEMU docs](https://www.qemu.org/docs/master/system/invocation.html)

# /etc/netplan/50-cloud-init.yaml
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
