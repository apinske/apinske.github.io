# KVM on ARM64 (Raspberry Pi 4)

* [install Ubuntu](ubuntu-raspi.md)
* install QEMU
    * `sudo apt install qemu-system-arm qemu-efi-aarch64`
    * `sudo adduser ubuntu kvm`
    * `wget http://dl-cdn.alpinelinux.org/alpine/v3.12/releases/aarch64/alpine-virt-3.12.0-aarch64.iso`
    * `qemu-img create hd.raw 1G`
    * `qemu-system-aarch64 -nodefaults -nographic -machine virt -cpu host -accel kvm -m 1G -bios /usr/share/qemu-efi-aarch64/QEMU_EFI.fd -blockdev driver=file,node-name=cd,filename=alpine-virt-3.12.0-aarch64.iso -device virtio-blk-device,drive=cd -chardev stdio,id=screen,mux=on,signal=off -serial chardev:screen -monitor chardev:screen -netdev user,id=net,net=10.0.0.0/24 -device virtio-net-device,netdev=net -blockdev driver=file,node-name=hd,filename=hd.raw -device virtio-blk-device,drive=hd`
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
