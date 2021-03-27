# OCP on Hetzner Server
## Rescue Installation
* `installimage -n srv1.pinske.dev -r yes -l 1 -d sda,sdb -p /boot:ext2:512M,/:ext4:all -i /root/images/CentOS-83-64-minimal.tar.gz -t yes -a`

## Setup
* `useradd apinske`
* `cp -R /root/.ssh /home/apinske/`
* `chown -R apinske:apinske /home/apinske/.ssh`
* `echo "apinske ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/apinske`
* `cp /etc/resolv.conf /root/resolv.conf` (Backup)

## CRC
* `wget https://mirror.openshift.com/pub/openshift-v4/clients/crc/latest/crc-linux-amd64.tar.xz`
* `mkdir -p .local/bin && tar --strip-components=1 -C .local/bin -xf crc-linux-amd64.tar.xz '*crc'`
* `crc config set memory 32768`
* `crc config set disk-size 256`
* `crc setup`
* `sudo sh -c 'cat /root/resolv.conf >> /etc/resolv.conf'`
* `crc start`
* `ssh -i ~/.crc/machines/crc/id_ecdsa core@$(crc ip)`
