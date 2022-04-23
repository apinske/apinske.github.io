# Ubuntu on Raspberry Pi

* `curl https://cdimage.ubuntu.com/releases/22.04/release/ubuntu-22.04-preinstalled-server-arm64+raspi.img.xz | xz -d - > sd.img`
* `diskutil unmountDisk /dev/disk2`
* `sudo dd bs=1m if=sd.img of=/dev/rdisk2`
* append ` modules-load=g_ether` to `/Volumes/system-boot/cmdline.txt`
* config network in `/Volumes/system-boot/network-config`
* config user in `/Volumes/system-boot/user-data`
* `diskutil eject /dev/disk2`
* boot, ssh
* update: `sudo apt update && sudo apt upgrade`

## network-config for USB-C Ethernet
```yaml
ethernets:
  # ...
  usb0:
    dhcp4: false
    addresses: [10.55.0.1/24]
```
