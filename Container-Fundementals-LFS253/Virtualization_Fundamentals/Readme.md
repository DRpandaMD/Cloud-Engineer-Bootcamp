# Virtualization Fundamentals

## Objectives

* Understand the `cgroups` concept.
* Understand the namespaces concept.
* Understand the UnionFS concept and the Copy-on-Write mechanism.
* Suspend and resume `cgroups`.
* Isolate network namespaces.
* Build a UnionFS from individual branches.


## Control Groups (cgroups)

* Control groups, known as **cgroups**, are a feature of the Linux kernel allowing the limitation, accounting, and isolation of resources used by groups of processes and their subgroups.

* `Cgroups` allow the limitation of memory, disk I/O, and network usage for a group of processes

* Cgroups allow the limitation of memory, disk I/O, and network usage for a group of processes

* a group's (`cgroup`) resource usage can be measured for accounting and billing purposes, and its state can be controlled by freezing and restarting the group

## Namespaces

* **Namespaces** are a feature of the Linux kernel allowing groups of processes to have limited visibility of the host system resources.

* Namespaces may limit the visibility of cgroups, hostname, process IDs, IPC mechanisms, network interfaces and routes, users, and mounted file systems

* an isolated process running inside a namespace, a namespaced resource, such as the network, will appear as the process' own dedicated resource

* Processes running inside a namespace are aware of any changes in the local namespaced system resources, however, such changes will not be visible to *other* processes or other namespaces


## Unification File System (UnionFS)

* [UnionFS](https://en.wikipedia.org/wiki/UnionFS) is a feature found in the Linux, FreeBSD and NetBSD kernels, allowing the overlay of separate transparent file systems to produce an apparent single unified file system

* When the content of several file systems, called branches, are virtually stacked, their contents appear to be merged, however, physically, they remain separate


## CGroup Lab 1

These labs were very abstract and not very fun 2/10
