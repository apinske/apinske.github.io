# podman on macOS
* on macOS
  * get VM: `curl --output virt.tar.gz https://github.com/apinske/simpleos/releases/download/v0.2/virt_$(uname -m).tar.gz && tar xf virt.tar.gz`
  * run VM: `./virt`
* in VM
  * `./setup-podman.sh` (1st time: install and reboot VM)
  * `./setup-podman.sh` (2nd time: print setup for macOS, e.g. `export CONTAINER_HOST=tcp://192.168.64.7:58080`)
  * to test: `podman run --rm -it --net host docker.io/alpine`
* on macOS
  * `brew install podman`
  * `export CONTAINER_HOST=tcp://192.168.64.7:58080` (see above)
  * to test: `podman run --rm -it --net host docker.io/alpine`
* current limitations:
  * only supports `--net none` and `--net host`
  * no file sharing, maybe try nfs:
    * on macOS: `mkdir /private/tmp/virt && sudo sh -c 'echo "/private/tmp/virt 192.168.64.7" >> /etc/exports && nfsd update'`
    * in VM: `mkdir -p /tmp/virt && mount -t nfs -o ro,vers=3,proto=tcp,nolock 192.168.64.1:/private/tmp/virt /tmp/virt`
  * probably many more...
