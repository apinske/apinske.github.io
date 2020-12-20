# Ubuntu on macOS (Virtualization.framework)

* prepare VM, on macOS
  * `curl -L --output virtual https://github.com/kendfinger/virtual/releases/download/v0.0.2/virtual`
  * `chmod +x virtual`
  * `curl --output cloudimg.tar.gz https://cloud-images.ubuntu.com/groovy/current/groovy-server-cloudimg-arm64.tar.gz`
  * `curl --output initrd https://cloud-images.ubuntu.com/groovy/current/unpacked/groovy-server-cloudimg-arm64-initrd-generic`
  * `curl --output vmlinuz.gz https://cloud-images.ubuntu.com/groovy/current/unpacked/groovy-server-cloudimg-arm64-vmlinuz-generic`
  * `gunzip vmlinuz.gz`
  * `tar xf cloudimg.tar.gz groovy-server-cloudimg-arm64.img`
  * `mv groovy-server-cloudimg-arm64.img vda.img`
  * `dd if=/dev/zero bs=1m count=9000 >> vda.img`
  * `mkdir cidata`
  * `echo '{"instance-id":"iid-local01","local-hostname":"ubuntu"'} > cidata/meta-data`
  * `echo -e '#cloud-config\npassword: ubuntu\nchpasswd: { expire: False }\nssh_pwauth: True' > cidata/user-data`
  * `hdiutil makehybrid -o cloud-init cidata -iso -joliet`
  * `rm -rf cidata`
  * `./virtual run -k vmlinuz -r initrd -d vda.img -d cloud-init.iso -c 'console=hvc0 root=/dev/vda' -p 2 -m 2048 --network`
* prepare VM, in Ubuntu
  * login with ubuntu/ubuntu
  * `sudo touch /etc/cloud/cloud-init.disabled`
  * `sudo poweroff`
* prepare VM, back on macOS
  * `reset`
  * `rm cloud-init.iso`
* use VM
  * `./virtual run -k vmlinuz -r initrd -d vda.img -c 'console=hvc0 root=/dev/vda' -p 2 -m 2048 --network`

## cloud-init-user-data
```
#cloud-config
password: ubuntu
chpasswd: { expire: False }
ssh_pwauth: True
```
