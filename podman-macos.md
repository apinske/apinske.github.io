# podman on macOS
* on macOS
  * get VM: `curl -Lo - https://github.com/apinske/virt/releases/download/v0.5/virt_$(uname -m).tar.gz | tar xf -`
  * run VM: `./virt`
* in VM
  * `./setup-vdb.sh`
  * `./setup-podman.sh`
  * to test: `podman run --rm -it alpine`
  * $HOME is mounted at /mnt/virt/home
  * `poweroff` to stop
