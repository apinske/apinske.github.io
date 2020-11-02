# k3d on Raspberry Pi

* install [Ubuntu](ubuntu-raspi.md)
* install docker
    * `curl https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
    * `sudo add-apt-repository "deb [arch=arm64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"`
    * `sudo apt install docker-ce docker-ce-cli containerd.io`
* install k3d
    * `wget -O k3d-linux-arm64 https://github.com/rancher/k3d/releases/download/v3.1.5/k3d-linux-arm64`
    * `sudo mv k3d-linux-arm64 /usr/local/bin/k3d`
    * `sudo chmod +x /usr/local/bin/k3d`
* add `cgroup_memory=1 cgroup_enable=memory` to `/boot/firmware/cmdline.txt`
* *optional:* install kubectl
    * `curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -`
    * `sudo add-apt-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"`
    * `sudo apt install kubectl`
* `sudo reboot`
* setup k3s cluster
    * `sudo k3d cluster create raspi -a 1 -p 80:80@loadbalancer`
    * `sudo k3d kubeconfig get -a`
    * move to local machine, replace 0.0.0.0 with host-name
* samples
    * `kubectl run -i -t gcc --image=gcc --rm --command -- /bin/bash -i -l`
* build k3s
    * `apt install make`
    * `git clone https://github.com/rancher/k3s.git`
    * `make`
* run built k3s
    * find image id
    * `k3d cluster create debug -a 1 -p 80:80@loadbalancer -i c4e1c8d53e11`

## .bashrc
```bash
if command -v tmux &> /dev/null && [ -z "$TMUX" ]; then
  tmux attach -t default || tmux new -s default
fi
```

## .tmux.conf
```
set -g prefix C-a
bind C-a send-prefix
unbind C-b
```

## hello-kubernetes.yaml
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
