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
    * `sudo chmod +x /usr/local/bin/k3d`
* add `cgroup_memory=1 cgroup_enable=memory` to `/boot/firmware/cmdline.txt`
* optional: install kubectl
    * `curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -`
    * `sudo add-apt-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"`
    * `sudo kubectl`
* `sudo reboot`
* setup k3s cluster
    * `sudo k3d create cluster raspi -w 1 -p 80:80@loadbalancer`
    * `sudo k3d get kubeconfig -a -o -`
    * move to local machine, replace 0.0.0.0 with host-name

> .bashrc
```bash
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

> hello-kubernetes.yaml
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-kubernetes
  labels:
    app: hello-kubernetes
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: hello-kubernetes
          servicePort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: hello-kubernetes
  labels:
    app: hello-kubernetes
spec:
  type: ClusterIP
  ports:
    - name: http
      port: 80
      targetPort: 80
  selector:
    app: hello-kubernetes
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-kubernetes
  labels:
    app: hello-kubernetes
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-kubernetes
  template:
    metadata:
      labels:
        app: hello-kubernetes
    spec:
      containers:
      - name: hello-kubernetes
        image: nginx
        ports:
        - containerPort: 80
```
