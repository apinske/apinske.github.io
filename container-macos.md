# container on macOS
## Setup
* `brew install container`
* `sudo container system dns create ctr`

## ~/.config/container/config.toml
```
[dns]
domain = "ctr"
```

## Docker
* `brew install docker`
* `container run -m 4G -c 2 --rosetta --name docker --cap-add ALL -v docker:/var/lib/docker -v ~/Documents/repos:/mnt/repos -d docker dockerd --tls=false --host=tcp://0.0.0.0:2375 --host=unix:///var/run/docker.sock`
  * `--insecure-registry ...`
* `docker context create ctr --docker "host=tcp://docker.ctr:2375"`
* `docker use ctr`
* `docker ps -a`

## ~/.testcontainers.properties
```
tc.host=tcp://docker.ctr:2375
testcontainers.reuse.enable=true
```

## k3s
* `brew install k3d kubernetes-cli`
* `k3d cluster create --api-port docker.ctr:random`
* `kubectl get pods -A`
