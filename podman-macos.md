# podman on macOS
* `brew install container`
* `sudo container system dns create ctr`
* `container system property set dns.domain ctr`
* `container run -m 4G -c 2 --rosetta --name podman -v podman:/var/lib/containers -d quay.io/podman/stable podman system service --time=0 tcp://0.0.0.0:2375`
* `podman system connection add -d ctr tcp://podman.ctr:2375`
* `podman ps -a`
