# KVM on ARM64 (Raspberry Pi 4)

* [Raspberry Pi Downloads](https://www.raspberrypi.org/downloads/)
* Ubuntu 20.04.1 LTS (64bit)
* wait for auto-update to complete: `tail -f /var/log/unattended-upgrades/unattended-upgrades-dpkg.log`
* update: `sudo apt update && sudo apt upgrade`
* install QEMU
    * `sudo apt install qemu-system-arm qemu-efi-aarch64`
    * `sudo adduser ubuntu kvm`
    * `wget http://dl-cdn.alpinelinux.org/alpine/v3.12/releases/aarch64/alpine-virt-3.12.0-aarch64.iso`
    * `qemu-system-aarch64 -nodefaults -nographic -machine virt -cpu host -accel kvm -m 1G -bios /usr/share/qemu-efi-aarch64/QEMU_EFI.fd -blockdev driver=file,node-name=cd,filename=alpine-virt-3.12.0-aarch64.iso -device virtio-blk-device,drive=cd -chardev stdio,id=screen,mux=on,signal=off -serial chardev:screen -monitor chardev:screen -netdev user,id=net,net=10.0.0.0/24 -device virtio-net-device,netdev=net`
* [QEMU docs](https://www.qemu.org/docs/master/system/invocation.html)
