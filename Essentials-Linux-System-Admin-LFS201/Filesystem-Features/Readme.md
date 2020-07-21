# Filesystem Features: attributes, Creating, Checking Mounting

[Home](/README.md)

## What is expected 

* Explain concepts such as directory files and extended attributes.
* Create and format filesystems.
* Check and fix errors on filesystems.
* Mount and unmount filesystems.​
* Use network file systems.
* Understand how to automount filesystems and make sure they mount at boot.

## lsattr and chattr -- List and Change Attributes 

Extended Attributes associate metadata not interpreted directly by the filesystem with files. Four namespaces exist: user, trusted, security, and system. The system namespace is used for Access Control Lists (ACLs), and the security namespace is used by SELinux

Flag values are stored in the file inode and may be modified and set only by the root user. They are viewed with **lsattr** and set with **chattr**

### inode

What information is not stored on the inode? -- File name

### Flags:

* i: Immutable
  * A file with the immutable attribute cannot be modified (not even by root). It cannot be deleted or renamed. No hard link can be created to it, and no data can be written to the file. Only the superuser can set or clear this attribute

* a: Append-only
  * A file with the append-only attribute set can only be opened in append mode for writing. Only the superuser can set or clear this attribute

* d: No-dump
  * A file with the no-dump attribute set is ignored when the dump program is run. This is useful for swap and cache files that you don't want to waste time backing up

* A: No atime update 
  * A file with the no-atime-update attribute set will not modify its atime (access time) record when the file is accessed but not otherwise modified. This can increase the performance on some systems because it reduces the amount of disk I/O on the system

## mkfs

Every filesystem type has a utility for formatting (making) a filesystem on a partition. The generic name for these utilities is mkfs. 

General format `mkfs [-t fstype] [options] [device-file]`

## fsck

utility designed to check for errors (and hopefully fix any that are found). The generic name for these utilities is fsck

General Format : `fsck [-t fstype] [options] [device-file]`

Note that journalling filesystems are much faster to check than older generation filesystems for two reasons:

* You rarely need to scan the entire partition for errors, as everything but the very last transaction has been logged and confirmed, so it takes almost no time to check.​

* Even if you do check the whole filesystem, newer filesystems have been designed with fast fsck in mind; older filesystems did not think much about this when they were designed as sizes were much smaller.

## Mounting Filesystems

* All accessible files in Linux are organized into one large hierarchical tree structure with the head of the tree being the root directory (/)

* it is common to have more than one partition (each of which can have its own filesystem type) joined together in the same filesystem tree

* The mount program allows attaching at any point in the tree structure; `umount` allows detaching them. 

* The mount point is the directory where the filesystem is attached

* It must exist before mount can use it; `mkdir` can be used to create an empty directory

* Each filesystem is mounted under a specific directory

* `sudo mount -t ext /dev/sdb4 /home` -- meaning:
  * Mounts an ext4 filesystem
  * Usually not necessary to specify the type with the -t option
  * The filesystem is located on a specific partition of a hard drive (/dev/sdb4)
  * The filesystem is mounted at the position /home in the current directory tree
  * Any files residing in the original /home directory are hidden until the partition is unmounted.

## more on mount

* format for the mount command : `mount [options] <source> <directory>`

* Labels are assigned by filesystem type specific utilities, such as e2label, and UUIDs are assigned when partitions are created as containers for the filesystem, and formatted with mkfs.

## listing currently mounted file systems

* run `mount`

## using umount

* to un-mount filesystems run : `umount [device-file | mount-point]`

* some examples:
  * Unmount the /home filesystem: `sudo umount /home`
  * unmount the /dev/sda3 device `sudo umount /dev/sda3`

## Network Shares (NFS)

* [Arch Linux NFS Wiki](https://wiki.archlinux.org/index.php/NFS)

* It is common to mount remote filesystems through network shares, so they appear as if they were on the local machine. Probably the most common method used historically has been NFS

* NFS was originally developed by Sun Microsystems in 1989, and has been continuously updated. Modern systems use NFSv4, which has been continuously updated since 2000.

* Other network filesystems include AFS (Andrew File System), and SMB (Server Message Book), also termed CIFS (Common Internet File System).  *note: from other coworkers it seems as SMB and CIFS tends to be a bit less stable than NFS.  Here natively Azure kinda falls short where AWS and Gcloud both have Cloud Native NFS services*


## Automatic Filesytem Mounting

* Linux systems have long had the ability to mount a filesystem only when it is needed. Historically, this was done using `autofs`

* This utility requires installation of the `autof` package using the appropriate package manager and configuration of files in /etc

* `autofs` is very flexible and well understood, systemd-based systems come with `automount` facilities built into the framework

## Labs -- to be completed