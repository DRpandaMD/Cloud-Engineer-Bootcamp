# Virtualization Mechanisms

* [Home](/README.md)


## End Goals

* Contrast and compare operating system-level and full virtualization concepts.

* Understand OS-level virtualization mechanisms: chroot, FreeBSD jails, Solaris zones, OpenVZ, 

* Linux containers (LXC), and systemd-nspawn.

* Use `chroot` in conjunction with `debootstrap`.

* Create unprivileged and privileged containers with LXC.

* Run a container with `systemd-nspawn` and manage it with systemd.


## Virtualization vs Containers

* with Virtualization (full) you'd need some type of hypervisor (hyper-v or VMware) to take up a physical server with a OS 

* Then you will need to load additionally FULL OSes ontop of they hypervisor , then layer your app on top.

* With OS level Virtualization containers can run with just App code and the runtime and whatever shared libraries on a single OS

## Operating System-Level Virtualization