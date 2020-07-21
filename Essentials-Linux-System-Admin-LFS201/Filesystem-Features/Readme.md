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

* The mount program allows attaching at any point in the tree structure; umount allows detaching them. 