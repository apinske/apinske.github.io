# Linux Containers
* [Container vs. VM](https://www.pngfind.com/pngs/m/385-3851955_the-correct-containers-vs-vms-image-docker-container.png)
  * shared kernel
  * container = process
* Kernel features
  * [namespaces](https://man7.org/linux/man-pages/man7/namespaces.7.html) (mount="NS", uts=hostname, time, ipc, user, pid, net)
  * cgroups (v1 vs v2)
* network
* overlay fs
* docker/podman (UI/build), containerd (Net/Images), runc (Runtime); lxc
   * image (tar ball)
* unshare/nsenter
* Container from scratch [sample](https://github.com/apinske/simpleos/tree/master/linux/x86)
