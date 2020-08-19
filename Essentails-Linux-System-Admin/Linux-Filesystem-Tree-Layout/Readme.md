# Linux File System Tree Layout

## Navigation

* [Home](/README.md)
* [Back](/Essentials-Linux-System-Admin-LFS201/Readme.md)

## Filesystem Hierarch Standard (FHS)

* see the guide line for the [Filesystem Hierarchy Standard](https://refspecs.linuxfoundation.org/FHS_3.0/fhs-3.0.pdf)

### Table : Main Directories

| DIRECTORY |	IN FHS? |	PURPOSE |
|-----------| --------- | ----------- |
| /	| Yes  | Primary directory of the entire filesystem hierarchy.|
| /bin |Yes |  Essential executable programs that must be available in single user mode.|
| /boot |Yes | Files needed to boot the system, such as the kernel, initrd or initramfs images, and boot configuration files and bootloader programs. |
| /dev | Yes | Device Nodes, used to interact with hardware and software devices.|
| /etc | Yes | System-wide configuration files.|
| /home | Yes | User home directories, including personal settings, files, etc.|
| /lib | Yes | Libraries required by executable binaries in /bin and /sbin.|
| /lib64 | No | 64-bit libraries required by executable binaries in /bin and /sbin, for ystems which can run both 32-bit and 64-bit programs. |
| /media | Yes | Mount points for removable media such as CDs, DVDs, USB sticks, etc.|
| /mnt | Yes | Temporarily mounted filesystems.|
| /opt | Yes | Optional application software packages.|
| /proc| Yes | Virtual pseudo-filesystem giving information about the system and processes running on it. Can be used to alter system parameters.|
| /sys | No | Virtual pseudo-filesystem giving information about the system and processes running on it. Can be used to alter system parameters. Similar to a device tree and is part of the Unified Device Model.|
| /root | Yes | Home directory for the root user.|
| /sbin | Yes | Essential system binaries.|
| /srv | Yes | Site-specific data served up by the system. Seldom used.|
| /tmp | Yes | Temporary files; on many distributions lost across a reboot and may be a ramdisk in memory. |
| /usr | Yes | Multi-user applications, utilities and data; theoretically read-only. |
| /var | Yes | Variable data that changes during system operation. |

## The Root (/) Directory

* special and is often in a special dedicate partion, with other components getting mounted later like:
  * `/home`
  * `/var`
  * `/opt` 

* root partition must contain all essential files required to boot the system 

* Then be able to mount other file systems

* It will need : {utilities, configuration files, boot loader information other essential startup data}

* It must be able to do the following:
  * Boot the system
  * restore the system from system backups on external media or from a NAS (something like this)
  * recover or repair the system -- a maintainer needs to have the tools to diagnose and rebuild the damaged system'


## /bin

* obviously important:
  * contains executables and scripts to be by system admins and normal users
  * contains executables that are used indirectly by scripts like `/bin/bash` something I use all the time when sshing or connecting to a container

### Required programs for /bin/

```bash
cat, chgrp, chmod, chown, cp, date, dd, df, dmesg, echo
false, hostname, kill, ln, login, ls, mkdir, mknod, more,
mount, mv, ps, pwd, rm, rmdir, sed, sh, stty, su, sync,
true, umount and uname
```

### Notes

* Command binaries that are deemed nonessential enough to merit a place in `/bin` go in `/usr/bin`. Programs required only by non-root users are placed in this category.

*Interestingly:*

* Some very recent distributions are abandoning the strategy of separating `/bin` and `/usr/bin` (as well as /sbin and `/usr/sbin`) and just have one directory with symbolic links, thereby preserving a two directory view. They view the time-honored concept of enabling the possibility of placing `/usr` on a separate partition to be mounted after boot as obsolete.

## /boot

* the directory for booting the SYSTEM, obviously `:D`

* **IMPORTANT** these two files are a must
  * `vmlinuz` -- the compressed linux kernel
  * `initramfs` -- "initial RAM filesystem" which is mounted before the real root filesystem becomes available.
  * `initrd` -- is the old version of initramfs

* `/boot` stores the data used before the kernel begins executing user-mode programs.

* It also includes two files for config and debugging
  * `config` -- used to configure the kernel compilation 
  * `System.map` -- the kernel symbol table, used for debugging 

## /dev

* Contains special device files or device nodes.  These represent devices built into or connected to the system.

* These files are needed for the system to work properly

* examples 
  * byte-stream
  * block I/O devices
  * Network devices are referenced by name (which you should have seen at some point) `eth1` `wlan0`

* modern linux distros used the `udev` system which creates nodes in `/dev/` only as need when devices are found

* you can look at more about udev [here](https://wiki.debian.org/udev#:~:text=udev%20is%20a%20replacement%20for,was%20executed%20in%20kernel%20space)

## /etc

* contains machine-local config files and start up scripts

* have a look at what is in `/etc/`
```bash
csh.login, exports, fstab, ftpusers, gateways,
gettydefs, group, host.conf, hosts.allow, 
hosts.deny, hosts,equiv, hosts.lpd, inetd.conf, 
inittab, issue, ld.so.conf, motd, mtab, mtools.conf,
networks, passwd, printcap, profile, protocols,
resolv.conf, rpc, securetty, services, shells, 
syslog.conf.
```

* linux has been around for a while and some are irrelevant

### Other subdirectories

* `/etc/skel` -- contains skeleton files used to populate newly created home directories.

* `/etc/systemd/` -- contains or points to configuration scripts for starting stopping system services when using systemd

* `/etc/init.d` -- contains start up and shut down scripts when using System V initialization 
  * check out this [link](https://linuxjourney.com/lesson/sysv-overview) to find out more about System V

## /home

* this is where the user directories go

* `/home/rick/`

* `/home/morty/`

* `/home/archer/`

* all the personal config, data and executables go here


## /lib and /lib64

* should contain only those libraries needed to execute the binaries in `/bin` and `/sbin`

* important for booting the system and executing commands within the root files system

* important to note: systems that support 32bit and 64bit must keep both types on the system

* for RHEL they separate them out in `/lib/` and `/lib64`


## /media

* used to mount filesystems and removable media (surprised?)
  * CDs
  * DVDs
  * USB drives
  * ....and somewhere in ancient history floppies 


## /mnt

* used by the SA to mount a filesystem when needed

* like NFS, Samba, CIFS, or AFS -- full disclosure the only one I know is NFS 


## /opt

* the directory desinged for software packages that wish to keep all or most of thier files in onie isolated place.

* if I had a app called **my_app**
  * `/opt/my_app/bin/` -- for binaries
  * `/opt/my_app/man/` -- for man pages

## /proc

* the directory for a pseudo-filesystem, where all information lives here only in the memory. like `/dev/`, `/proc` is empty on a non running system

* `/proc/interrutps`

* `/proc/meminfo`

* `/proc/mounts`

* `/proc/partitions`

## /sys

* similar to `/proc/` as its a pseudo filesystem

* one lined system information is held here

## /root

* its the home directory for the root user

## /sbin

* contains binaries essential for booting, restoring, recovering or repairing in addition to those found in `/bin/`

## /srv

*"/srv contains site-specific data which is served by this system.*

*This main purpose of specifying this is so that users may find the location of the data files for particular service, and so that services which require a single tree for readonly data, writable data and scripts (such as cgi scripts) can be reasonably placed.*

*The methodology used to name subdirectories of /srv is unspecified as there is currently no consensus on how this should be done. One method for structuring data under /srv is by protocol, e.g. ftp, rsync, www, and cvs."*

So apparently there is some confusion here. As other software like Apache Web Server just use /var/www/ to serve content out of.

Not 100% sure what to do about this information just than know it just in case `:D`

## /tmp

This is the directory used to store temporary files, and can be accessed by an suer or app.  Keep in mind that files in `/tmp/` cannot be depended on to stay around for very long.

* Some distributions run automated cron jobs, which remove any files older than 10 days typically, unless the purge scripts have been modified to exclude them.

* Some distributions remove the contents of /tmp with every reboot. This has been the Ubuntu policy.

* Some modern distributions utilize a virtual filesystem, using the /tmp directory only as a mount point for a ram disk using the tmpfs filesystem. This is the default policy on Fedora systems. When the system reboots, all information is thereby lost; /tmp is indeed temporary!

## /usr

* often thought as th a secondary hierarchy

* used for files that are not need for system booting

* `/usr` does not need to reside in the same partition as the root directory

*Interestingly* you can find that:

"Some recent distributions are abandoning the strategy of separating /bin and /usr/bin (as well as /sbin and /usr/sbin) and just have one directory with symbolic links, thereby preserving a two directory view. They view the time-honored concept of enabling the possibility of placing /usr on a separate partition to be mounted after boot as obsolete."


### Directories under /usr

|DIRECTORY   | PURPOSE |
| ---------- | -------- |
|/usr/bin |Non-essential command binaries|
|/usr/etc |Non-essential configuration files (usually empty)|
|/usr/games | Game data|
|/usr/include |Header files used to compile applications|
|/usr/lib | Library files|
|/usr/lib64 | Library files for 64-bit|
|/usr/local | Third-level hierarchy (for machine local files)|
|/usr/sbin | Non-essential system binaries|
|/usr/share | Read-only architecture-independent files|
|/usr/src | Source code and headers for the Linux kernel|
|/usr/tmp | Secondary temporary directory|

## /var

The directory that contains variable data files. Some examples of these types of files that change during system runtime:

* log files

* Spool directories and files

* Administrative data files

* Transient and temporary files, such as cache contents.

*For Security* its often consider a good idea to mount `/var` as a separate filesystem.  Also, if the directory gets full, it should NOT lock up the system.

### Table for directories under /var

|SUBDIRECTORY | PURPOSE |
| ------------ | -------- |
|/var/ftp | Used for ftp server base|
|/var/lib | Persistent data modified by programs as they run |
|/var/lock | Lock files used to control simultaneous access to resources |
|/var/log | Log files |
|/var/mail | User mailboxes |
|/var/run | Information about the running system since the last boot |
|/var/spool | Tasks spooled or waiting to be processed, such as print queues |
|/var/tmp | Temporary files to be preserved across system reboot. Sometimes linked to /tmp |
|/var/www | Root for website hierarchies |

## /run

* used to store transient files

* files that may need to be written early in system startup, but don't need to be preserved in a reboot

* generally implemented as an empty mount point

* mount with tmpfs ram disk so behaves like a psuedo-filesystem existing only in memory


## Labs

### Lab: Sizes of the Default Linux Directories

For this I just set up a `ubuntu 18.04 bionic` on a baby `f1-micro` in GCP

Then you just hop over to the cloud shell

Type in `gcloud compute instances list` and then `gcloud compute ssh ubuntu01 --zone=us-central1-a` and boom you are ssh'd in 

You are asked to get the  size of the files in the top level directory

Use : `sudo du --max-depth=1 -hx /` 

* du is disk usage

* --max-depth=n is --max-depth=N     print the total for a directory (or file, with --all)
                          only if it is N or fewer levels below the command
                          line argument;  --max-depth=0 is the same as
                          --summarize

* -h is for human readable -x is on one file system (skips things on different file systems)


### Lab: Touring the /proc Filesystem

```bash

cd /proc
ls -F
cat /proc/cpuinfo
cat /proc/meminfo
cat /proc/mounts
cat /proc/swaps
cat /proc/version
cat /proc/partitions
cat /proc/interrupts
```


## Questions

According to Filesystem Hierarchy Standard (FHS), which top level directory (under /, such as /tmp, not /tmp/other) should be used for system-wide configuration files for all users?

* /etc

According to Filesystem Hierarchy Standard (FHS), which op level directory (under /, such as /tmp, not /tmp/other) should contain individual user files, such as settings and stored data?

* /home

According to Filesystem Hierarchy Standard (FHS), which top level directory (under /, such as /tmp, not /tmp/other) contains system log files, such as system service messages and web server logs?

* /var

According to Filesystem Hierarchy Standard (FHS), which top level directory (under /, such as /tmp, not /tmp/other) should contain files that are used only for the early stages of system startup, including the compressed Linux kernel (vmlinuz) and initial ram filesystem (initramfs)?

* /boot


According to Filesystem Hierarchy Standard (FHS), which top level directory (under /, such as /tmp, not /tmp/other) should contain Linux kernel modules (dynamically loaded and unloaded extensions for the kernel)?

* /lib

There are some directories that contain special entries which abstract system properties, instead of data stored on the disk. Those pseudo-files are very important for the system to function properly. Which of the following directories contain these? Select all that apply.

* /dev

* /proc

* /sys