# CentOS on Hetzner Server
## Rescue Installation
* `installimage -n srv1.pinske.dev -r yes -l 1 -d sda,sdb -p /boot:ext2:512M,/:ext4:all -i /root/images/CentOS-83-64-minimal.tar.gz -t yes -a`

## Setup
* `yum install NetworkManager`

## CRC
* `wget https://mirror.openshift.com/pub/openshift-v4/clients/crc/latest/crc-linux-amd64.tar.xz`
