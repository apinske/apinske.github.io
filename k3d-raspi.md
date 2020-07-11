# k3d on Raspberry Pi

* [Raspberry Pi Downloads](https://www.raspberrypi.org/downloads/)
* Ubuntu 20.04 LTS (64bit)
* wait for auto-update to complete: `tail -f /var/log/unattended-upgrades/unattended-upgrades-dpkg.log`
* update: `sudo apt update && sudo apt upgrade`
* install docker
    * `curl https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
    * `sudo add-apt-repository "deb [arch=arm64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"`
    * `sudo apt-get install docker-ce docker-ce-cli containerd.io`
* install k3d
    * `wget https://github.com/rancher/k3d/releases/download/v3.0.0-rc.6/k3d-linux-arm64`
    * `sudo mv k3d-linux-arm64 /usr/local/bin/k3d`
* add `cgroup_memory=1 cgroup_enable=memory` to `/boot/firmware/cmdline.txt`
* optional: install kubectl
    * `curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -`
    * `sudo add-apt-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"`
    * `sudo kubectl`
* `sudo reboot`
* setup k3s cluster
    * `k3d create cluster demo -w 1 -p 80:80@loadbalancer`

> .bashrc
```
if command -v tmux &> /dev/null && [ -z "$TMUX" ]; then
  tmux attach -t default || tmux new -s default
fi
```

> .tmux.conf
```
set -g prefix C-a
bind C-a send-prefix
unbind C-b
```
