# podman on macOS
* `curl --output virt.tar.gz https://github.com/apinske/simpleos/releases/download/v0.2/virt_$(uname -m).tar.gz`
* `tar xf virt.tar.gz`
* `./virt`
* `./setup-podman.sh`
* `podman run --rm -it --net host docker.io/alpine`
