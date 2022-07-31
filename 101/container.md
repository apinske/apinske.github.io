# Linux Containers
* [Container vs. VM](https://www.pngfind.com/pngs/m/385-3851955_the-correct-containers-vs-vms-image-docker-container.png)
  * shared kernel
  * container = process
  * DEMO docker
* Kernel features
  * [namespaces](https://man7.org/linux/man-pages/man7/namespaces.7.html) (mount="NS", uts=hostname, time, ipc, user, pid, net)
  * cgroups (v1 vs v2)
  * DEMO kernel config
* network
* overlay fs
* docker/podman (UI/build), containerd (Net/Images), runc (Runtime); lxc
* k8s-integration
* unshare/nsenter
* Container from scratch
  * DEMO `./run.sh`
