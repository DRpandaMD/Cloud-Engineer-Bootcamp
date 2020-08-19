# Linux Filesystems and the VFS

[Home](/README.md)

Learn this:

* Explain the basic filesystem organization.

* Understand the role of the VFS.

* Know what filesystems are available in Linux and which ones can be used on your actual system.

* Grasp why journaling filesystems represent significant advances.

* Discuss the use of special filesystems in Linux.


## Inodes

* Data structure on the disk that describes and stores file attributes:
  * Permissions​
  * User and group ownership​
  * Size​
  * Timestamps (nanosecond)
    * Last access time
    * Last modification time
    * Change time.


## Hard and Soft links

A directory file is a particular type of file that is used to associate file names and inodes. There are two ways to associate (or link) a file name with an inode:

* Hard links point to an inode.​ All hard linked files have to be on the same filesystem. Changing the content of a hard linked file in one place may not change it in other places.

* Soft (or symbolic) links point to a file name which has an associated inode. Soft linked files may be on different filesystems. If the target does not yet exist or is not yet mounting, it can be dangling.


## Virtual Filesystem (VFS)


Linux implements a Virtual File System (VFS), as do all modern operating systems. When an application needs to access a file, it interacts with the *VFS abstraction layer*, which then translates all the I/O system calls (reading, writing etc.) into specific code relevant to the particular actual filesystem.

Thus, *neither the specific actual filesystem or physical media and hardware on which it resides need be considered by applications*. Furthermore, *network filesystems (such as NFS) can be handled transparently*.

This permits Linux to work with more filesystem varieties than any other operating system. 


## Available File systems

* ext4: Linux native filesystem (and earlier ext2 and ext3)
* XFS: A high-performance filesystem originally created by SGI
* JFS: A high-performance filesystem originally created by IBM
* Windows-natives: FAT12, FAT16, FAT32, VFAT, NTFS
* Pseudo-filesystems resident only in memory, including proc, sysfs, devfs, debugfs
* Network filesystems such as NFS, coda, afs

You can find currently supported filesystem in `/proc/filesystems/`

* *`nodev` filesystems are pseudo-filesystems*

## Journaling Filesystems 

In a journaling filesystem, operations are grouped into transactions. A transaction must be completed without error, atomically; otherwise, the filesystem is not changed. A log file is maintained of transactions. When an error occurs, usually only the last transaction needs to be examined.

* ext3

* ext4

* reiserfs

* JFS

* XFS

* btrfs