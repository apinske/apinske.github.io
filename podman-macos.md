# podman on macOS
* on macOS
  * get VM: `curl -L --output virt.tar.gz https://github.com/apinske/simpleos/releases/download/v0.4/virt_$(uname -m).tar.gz && tar xf virt.tar.gz`
  * run VM: `./virt`
* in VM
  * `./setup-vdb.sh`
  * `./setup-podman.sh` (1st time: install and reboot VM)
  * `./setup-podman.sh` (2nd time: print setup for macOS, e.g. `export CONTAINER_HOST=tcp://192.168.64.7:58080`)
  * to test: `podman run --rm -it alpine`
  * $HOME is mounted at /mnt/virt/home
* on macOS
  * `brew install podman`
  * `export CONTAINER_HOST=tcp://192.168.64.7:58080` (see above)
  * to test: `podman run --rm -it alpine`
