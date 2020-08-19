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

* [OS-Level Virtualization wiki link](https://en.wikipedia.org/wiki/OS-level_virtualization)

* refers to a kernel’s capability to allow the creation and existence of multiple isolated virtual environments on the same host

* environments, known as containers, zones, partitions, virtual kernels, or jails, encapsulate running programs and conceal from them the true nature of their virtual environment, creating an illusion of a real computing environment

* opposed to programs running inside a real environment, where they see all resources such as CPU, network, connected devices, and files, programs running inside a virtual environment are limited to its content and assigned devices

* Operating System level virtualization is typically used to limit usage and securely isolate resources shared between multiple programs or users, and to separate programs to run in their own assigned virtual environments for better security and resource management

* Operating System level virtualization requires less overhead than full virtualization because everything is managed at the kernel level without the need to install a guest OS

* OS-level virtualization introduces a stacked storage management model, implemented by the file-level Copy-on-Write (CoW) mechanism

## Chroot

* [Chroot wiki link](https://en.wikipedia.org/wiki/Chroot)

* **Chroot** operates on a UNIX-like operating system by changing the apparent root directory of a process and its children

* This apparent root directory is no longer the real root directory of the operating system, it is a virtual root directory - commonly referred to as a **chrooted** directory

* **chroot jail** was first introduced in 1991, when the feature was used to build a honeypot to monitor the activity of crackers

* Chroot may be used to create a test environment to safely and securely test new software, to manage and isolate dependencies, and to recover an unbootable system

* only a root user may perform a chroot, it is not a defense mechanism against root attacks


## FreeBSD jails

* [FreeBSD Jail Wiki Link](https://en.wikipedia.org/wiki/FreeBSD_jail) 

FreeBSD jails mainly aim at three goals:

* Virtualization: Each jail is a virtual environment running on the host machine with its own files, processes, user and superuser accounts. From within a jailed process, the environment is almost indistinguishable from a real system.

* Security: Each jail is sealed from the others, thus providing an additional level of security.

* Ease of delegation: The limited scope of a jail allows system administrators to delegate several tasks which require superuser access without handing out complete control over the system.


FreeBSD jail allows for the partitioning of a FreeBSD system into many independent systems, called jails. They share the same kernel, but virtualize the system’s files and resources for improved security and administration through clean isolation between services.

FreeBSD jails is a mechanism that also may be used to prevent a chroot type of attack.


## Solaris Zones 

* [Solaris Zones Wiki link](https://en.wikipedia.org/wiki/Solaris_Containers)

A Solaris Container is the combination of system resource controls and the boundary separation provided by zones. Zones act as completely isolated virtual servers within a single operating system instance. By consolidating multiple sets of application services onto one system and by placing each into isolated virtual server containers, system administrators can reduce cost and provide most of the same protections of separate machines on a single machine

Processes running inside a zone are isolated from other zones and the rest of the host system. Direct communication is possible among processes within the same zone, however, communication with processes from other zones is only allowed through APIs


## OpenVZ 

* [OpenVZ Wiki Link](https://en.wikipedia.org/wiki/OpenVZ)

OpenVZ (Open Virtuozzo) is an operating-system-level virtualization technology for Linux. It allows a physical server to run multiple isolated operating system instances, called containers, virtual private servers (VPSs), or virtual environments (VEs). OpenVZ is similar to Solaris Containers and LXC.

While virtualization technologies such as VMware, Xen and KVM provide full virtualization and can run multiple operating systems and different kernel versions, OpenVZ uses a single Linux kernel and therefore can run only Linux. All OpenVZ containers share the same architecture and kernel version. This can be a disadvantage in situations where guests require different kernel versions than that of the host. However, as it does not have the overhead of a true hypervisor, it is very fast and efficient.



## Linux Containers (LXC)

* [Linux Containers (LXC) Wiki link](https://en.wikipedia.org/wiki/LXC)


LXC (Linux Containers) is an operating-system-level virtualization method for running multiple isolated Linux systems (containers) on a control host using a single Linux kernel.

The Linux kernel provides the cgroups functionality that allows limitation and prioritization of resources (CPU, memory, block I/O, network, etc.) without the need for starting any virtual machines, and also namespace isolation functionality that allows complete isolation of an application's view of the operating environment, including process trees, networking, user IDs and mounted file systems.

LXC combines the kernel's cgroups and support for isolated namespaces to provide an isolated environment for applications. Early versions of Docker used LXC as the container execution driver, though LXC was made optional in v0.9 and support was dropped in Docker v1.10

* Linux Containers, is a mechanism implementing OS-level virtualization; it was first introduced in 2008 on Linux systems

* LXC allows multiple isolated systems to run on a single Linux host, using chroot and cgroups, together with namespace isolation features of the Linux kernel to limit resources, set priorities, and isolate processes, the filesystem, network and users from the host operating system

## Systemd-nspawn

[sytemd-nspawn wikik](https://wiki.archlinux.org/index.php/Systemd-nspawn)

systemd-nspawn is like the chroot command, but it is a chroot on steroids.

systemd-nspawn may be used to run a command or OS in a light-weight namespace container. It is more powerful than chroot since it fully virtualizes the file system hierarchy, as well as the process tree, the various IPC subsystems and the host and domain name.

Systemd-nspawn may be used to run a simple script or boot an entire Linux-like operating system in a container. Systemd-nspawn fully isolates containers from each other and from the host system, therefore processes running in a container are not able to communicate with processes from other containers. It fully virtualizes the process tree, filesystem, users, host and domain name.
