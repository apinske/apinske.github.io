# Ubuntu on macOS (Virtualization.framework)

* Install Tools
  * `brew install kendfinger/tools/virtual coreutils`
* Run
  * `./run.sh`

## run.sh
```sh
#!/bin/sh
if [ ! -f vda.img ]; then
  ARCH=arm64
  if [ "x86_64" = "$(uname -m)" ]; then
    ARCH=amd64
  fi

  wget -O- https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-$ARCH.tar.gz | tar xzf - jammy-server-cloudimg-$ARCH.img
  wget -O initrd https://cloud-images.ubuntu.com/jammy/current/unpacked/jammy-server-cloudimg-$ARCH-initrd-generic
  wget -O vmlinuz https://cloud-images.ubuntu.com/jammy/current/unpacked/jammy-server-cloudimg-$ARCH-vmlinuz-generic
  file vmlinuz | grep gzip && gunzip -f -S '' vmlinuz

  mkdir cidata
  echo '#cloud-config\npassword: ubuntu\nchpasswd: { expire: False }\nssh_pwauth: True' > cidata/user-data
  echo '{"instance-id":"iid-local-01","local-hostname":"ubuntu"}' > cidata/meta-data
  hdiutil makehybrid -o cloud-init cidata -iso -joliet
  rm -rf cidata

  mv jammy-server-cloudimg-$ARCH.img vda.img
  truncate -s 10G vda.img
fi
virtual run -k vmlinuz -r initrd -d vda.img -d cloud-init.iso -c 'console=hvc0 root=/dev/vda' -p 2 -m 2048 -n -a 0a:00:00:00:00:0f
reset
```
