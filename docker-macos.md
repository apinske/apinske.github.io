# docker on macOS
## Setup
* `brew install container docker`
* `sudo container system dns create ctr`
* `container system property set dns.domain ctr`
* `container run -m 4G -c 2 --rosetta --name docker -v docker:/var/lib/docker -v ~/Documents/repos:/mnt/repos -d docker dockerd --tls=false --host=tcp://0.0.0.0:2375 --host=unix:///var/run/docker.sock`
  * `--insecure-registry ...`
* `docker context create ctr --docker "host=tcp://docker.ctr:2375"`
* `docker use ctr`
* `docker ps -a`

## ~/.testcontainers.properties
```
tc.host=tcp://docker.ctr:2375
testcontainers.reuse.enable=true
```
