# Container Storage

* [Home](/README.md)

## Things to understand

* Understand the Copy-on-Write feature of UnionFS.
* How to Create and mount volumes with Docker.
* How to Manage volumes with rkt.
* Understand storage management with CRI-O.


## UnionFS with Copy-on-Write

UnionFS is used by Docker to overlay a base container image with storage layers, such as ephemeral storage layer, custom storage layer, and config layer at the time a new container is created. The ephemeral storage is reserved for the container’s Input/Output (I/O) operations and it is not recommended to be used for persistent data; instead, a volume should be mounted on the container to provide persistent storage not managed by UnionFS

The Copy-on-Write (CoW) strategy of UnionFS allows users to “indirectly” modify the content of files available to the running container from the base container image storage layer. While the container image files are Read-Only, when a user attempts to modify such a file, no errors or mechanisms will prevent the user from doing so. Instead, the base container image file is copied and saved on the ephemeral storage layer of the container and the user is allowed to make changes to the new copy of the file. In essence, a copy of a file is being saved when a user attempts to edit a Read-Only file of the base container image, all while the base container image file remains intact

This strategy is used at the operating system level for memory management and process management, and it is used by Docker to manage the storage for container images, running containers, and to minimize I/O and the size of each storage layer.

## Docker Storage Drivers

typically, not a lot of data needs to be written to the container’s writable layer. However, when such a requirement presents itself, then a storage driver needs to be used to control how the container images and the running containers are stored and managed on the host system

While Docker supports a variety of storage drivers, each driver may be limited by its supported backing filesystems. The **overlay**, **overlay2** and **aufs** drivers are supported by xfs and ext4 filesystems, **devicemapper** driver is backed by **direct-lvm**, but **vfs** is supported by any filesystem. Despite the implementation and feature differences between storage drivers, they all use the CoW strategy for stacked storage layers


## Storage and Docker Volumes

Applications and containers make use of two different types of data - ephemeral and persistent; therefore Docker takes separate approaches when managing ephemeral vs. persistent data.

Ephemeral data is data typically needed for a very short period of time, and there is no damage to the enterprise when that data is lost once the container is terminated and deleted. Ephemeral data is stored on ephemeral storage, which is specified and defined within the scope of the container, and; the ephemeral storage also gets deleted together with the container. For ephemeral data management, Docker provides a bind mount mechanism tied in with the host’s directory structure. The bind mount references a directory of the host system created on-demand if not existing when the container is created. One of the downsides of bind mounts is that they cannot be directly managed from the Docker CLI.

It is recommended, however, the use of a tmpfs mount for ephemeral data, not only to prevent the data from being stored permanently on the container’s writable storage layer, but also to increase the container’s performance.

Persistent data, however, is critical data that has to be stored in a location that provides data resilience, to then later be managed by an aggregator or other applications designed to process and manage critical data. Persistent data is recommended to be stored on external persistent volume mounts managed from the Docker CLI for easy backup and migration, flexibility, portability, sharing, and extended functionality. Persistent volumes are not managed by UnionFS, are not specified and defined only within the scope of a container, and do not get deleted together with the container.

Docker users utilize volume plugins to store Docker volumes on remote hosts or cloud providers, to encrypt the contents of volumes, or to add other functionality. Such plugins are the Azure File Storage plugin, BeeGFS Volume Plugin, DigitalOcean Block Storage plugin, Flocker plugin, gce-docker plugin, GlusterFS plugin, VMware vSphere Storage plugin, etc.

## Labs -- to be done again.