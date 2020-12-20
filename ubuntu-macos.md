# Ubuntu on macOS (Virtualization.framework)

* prepare VM
  * on macOS
    * `curl -L --output virtual https://github.com/kendfinger/virtual/releases/download/v0.0.2/virtual`
    * `chmod +x virtual`
    * `curl --output cloudimg.tar.gz https://cloud-images.ubuntu.com/groovy/current/groovy-server-cloudimg-arm64.tar.gz`
    * `curl --output initrd https://cloud-images.ubuntu.com/groovy/current/unpacked/groovy-server-cloudimg-arm64-initrd-generic`
    * `curl --output vmlinuz.gz https://cloud-images.ubuntu.com/groovy/current/unpacked/groovy-server-cloudimg-arm64-vmlinuz-generic`
    * `gunzip vmlinuz.gz`
    * `tar xf cloudimg.tar.gz groovy-server-cloudimg-arm64.img`
    * `mv groovy-server-cloudimg-arm64.img vda.img`
    * `dd if=/dev/zero bs=1m count=9000 >> vda.img`
  * on another Ubuntu
    * apt install cloud-image-utils
    * cloud-localds -H ubuntu cloud-init.img cloud-init-user-data
  * on macOS
    * cloud-init.img
    * `./virtual run -k vmlinuz -r initrd -d vda.img -d cloud-init.img -c 'console=hvc0 root=/dev/vda' -p 2 -m 2048 --network`
  * in Ubuntu VM
    * login with ubuntu/ubuntu
    * `sudo touch /etc/cloud/cloud-init.disabled`
    * `sudo poweroff`
  * on macOS
    * `rm cloud-init.img`
* use VM
  * `./virtual run -k vmlinuz -r initrd -d vda.img -c 'console=hvc0 root=/dev/vda' -p 2 -m 2048 --network`

## cloud-init-user-data
```
#cloud-config
password: ubuntu
chpasswd: { expire: False }
ssh_pwauth: True
```
