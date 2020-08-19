# Disk Partitioning

[Home](/README.md)


## End Goals

* Describe and contrast the most common types of hard disks and data buses.
* Explain disk geometry and other partitioning concepts.
* Understand how disk devices are named and how to identify their associated device nodes.
* Distinguish among and select different partitioning strategies.
* Use utilities such as **fdisk**, **blkid** and **lsblk**.
* Back up and restore partition tables.


## Disk Types

* SATA ( Serial Advanced Technology Attachment)

* SCSI (Small Computer Systems Interface)

* SAS (Serial Attached SCSI)

* USB (Universal Serial Bus)

* SSD (Solid State Drives)

* IDE and EIDE (Integrated Drive Electronics, Enhanced IDE) -- Obsolete


## Disk Geometry

Disk Geometry is a concept with a long history for rotational media; one talks of heads, cylinders, tracks and sectors.

* `sudo fdisk -l /dev/sda` -l will just list the partition table 

* the physical structural image has been less and less relevant as internal electronics on the drive actually obscure much of it. Furthermore, SSDs have no moving parts


## Partition Organization 

Disks are divided into partitions. In geometrical terms, these consist of physically contiguous groups of sectors or cylinders. A partition is a physically contiguous region on the disk. There are two partitioning layouts in use:

* MBR (Master Boot Record)
* GPT (GUID Partition Table).

MSDOS era is where the MBR dates back to


GPT is on all modern systems and is based on UEFI (Unified Extensible Firmware Interface). By default, it may have up to 128 primary partitions. When using the GPT scheme, there is no need for extended partitions. Partitions can be up to 233 TB in size (with MBR, the limit is just 2TB).

## Why partition??

* Separation of user and application data from operating system files
* Sharing between operating systems and/or machines
* Security enhancement by imposing different quotas and permissions for different system parts
* Size concerns; keeping variable and volatile storage isolated from stable
* Performance enhancement of putting most frequently used data on faster storage media
* Swap space can be isolated from data and also used for hibernation storage.

A common partition layout contains a boot partition, a partition for the root filesystem /, a swap partition, and a partition for the /home directory tree.

Keep in mind that it is more difficult to resize a partition after the fact than during install/creation time. Plan accordingly.


## blkid

**blkid** is a utility to locate block devices and report on their attributes. It works with the **libblkid** library. It can take as an argument a particular device or list of devices

It can determine the type of content (e.g. filesystem, swap) a block device holds, and also attributes (tokens, NAME=value pairs) from the content metadata (e.g., LABEL or UUID fields).

blkid will only work on devices which contain data that is finger-printable: e.g., an empty partition will not generate a block-identifying UUID. blkid has two main forms of operation: either searching for a device with a specific NAME=value pair, or displaying NAME=value pairs for one or more devices. Without arguments, it will report on all devices. There are quite a few options designating how to specify devices and what attributes to report on


## lsblk

lsblk
A related utility is lsblk which presents block device information in a tree format


## Sizing up Partitions

A linux system to should use at minimum of two partions:

### /(root)

This is used for the entire logical filesystem

* In practice, most installations will have more than one filesystem on more than one partition, which are joined together at mount points.

* It is difficult with most filesystem types to resize the root partition; using LVM, which we will discuss later, can make this easier.

* While it is certainly possible to run Linux with just the root partition, most systems use more partitions to allow for easier backups, more efficient use of disk drives, and better security.

### swap 

* Swap is used as an extension of physical memory; pages of memory which are not file-backed can be moved to disk until needed again.

* The usual recommendation is swap size should be equal to physical memory in size; sometimes, twice that is recommended. However, the correct choice depends on the related issues of system use scenarios, as well as hardware capabilities. Examples of thinking on this subject can be found at the SwapFaq web page and in the Is a swap partition required for SLES? document.

* The system may have multiple swap partitions and/or swap files.

* On a single disk system, try to center the swap partition; on multiple disk systems, try to spread swap over disks.

* Adding more and more swap will not necessarily help because, at a certain point, it becomes useless. You will need to either add more memory or re-evaluate the system setup.

## using fdisk

**fdisk** will always be included in any Linux installation, so it is a good idea to learn how to use it. You must be root to run **fdisk**. It can be somewhat complex to handle, and caution is advised.

`sudo fdisk /dev/sdb`

commands:

* m: Display the menu
* p: List the partition table
* n: Create a new partition
* d: Delete a partition
* t: Change a partition type
* w: Write the new partition table information and exit
* q: Quit without making changes.