# Filesystem Features: Swap, Quotas, Usage

[Home](/README.md)

## What to Learn

* Explain the concepts of swap and quotas.
* Use the utilities that help manage quotas: `quotacheck`, `quotaon`, `quotaoff`, `edquota`, and `quota`.
* Use the utilities `df` and `du`.


## Using Swap

linux uses a virutal memory system with swap to give it more memory than it actually has.  This kind of memory overcommision functions in two ways:

* Many programs do not actually use all the memory they are given permission to use. Sometimes, this is because child processes inherit a copy of the parent's memory regions utilizing a COW (Copy On Write) technique, in which the child only obtains a unique copy (on a page-by-page basis) when there is a change.

* When memory pressure becomes important, less active memory regions may be swapped out to disk, to be recalled only when needed again.

swapping is usually done to one or more dedicated partitions or files

Linux permits multiple swap areas, so the needs can be adjusted dynamically

Each area has a priority, and lower priority areas are not used until higher priority areas are filled.

the recommended swap size is the total RAM on the system

You can see what your system is currently using for swap areas by looking at `/proc/swaps` and getting basic memory statistics with `free` 

The only commands involving swap are:

* `mkswap`: format a swap partition or file

* `swapon`: activate a swap partition or file

* `swapoff`: deactivate a swap partition or file.


Linux memory used by the kernel itself, as opposed to application memory, is never swapped out, in distinction to some other operating systems


## Filesystem Quotas

* `quotacheck` -- generates and updates quota accounting files

* `quotaon` -- enables quota accounting

* `quotaoff` -- disables quota accounting

* `edquota` -- used for editing user or group quotas

* `quota`  -- reports on usage and limits

## Set up Quotas 

To create a filesystem quota, you must first make sure you have mounted the filesystem with the user and/or group quota mount options. Without these, nothing else will work. The basic steps are:

* Mount the filesystem with user and/or group quota options:
  * Add the `usrquota` and/or `grpquota` options to the filesystems entry in `/etc/fstab`
  * Remount the filesystem (or mount it if new)

* Run `quotacheck` on the filesystem to set up quotas
* Enable quotas on the file system 
* Set quotas with the `edquota` program.


## quotacheck

The `quotacheck` utility creates and updates the quota accounting files `(aquota.user` and `aquota.group`) for the filesystem.

To update user files for all filesystems in `/etc/fstab` with user quota options:

* `$ sudo quotacheck -ua`

To update group files for all filesystems in `/etc/fstab` with group quota options:

* `$ sudo quotacheck -ga`

To update the user file for a particular filesystem:

* `$ sudo quotacheck -u [somefilesystem]`

To update the group file for a particular filesystem:

* `$ sudo quotacheck -g [somefilesystem]`

Use the -v option to get more verbose output.

quotacheck is generally only run when quotas are initially turned on (or need to be updated). The program may also be run when `fsck` reports errors in the filesystem when the system is starting up.

## turning Quotas on and off

Easy as :

* `sudo quotaon [flags] [filesystem]`

* `sudo quotaoff [flags] [filesystem]`

and the associated tags:

```bash
-a, --all                 turn quotas off for all filesystems

-f, --off                 turn quotas off

-u, --user                operate on user quotas

-g, --group               operate on group quotas

-p, --print-state         print whether quotas are on or off

-x, --xfs-command=cmd     perform XFS quota command

-F, --format=formatname   operate on specific quota format

-v, --verbose             print more messages

-h, --help                display this help text and exit

-V, --version             display version information and exit
```

## Examining Quotas

Use `quota` to generate reports

* `quota -u` will return the user quota

* `quota -g` will return the group quota 

## Setting Quotas

Use `edquota` to bring up the quota editor.

Some Examples: 

* edquota -u [username] edits limits for username
  * `sudo edquota -u [username]`

* edquota -g [groupname] edits limits for groupname
  * `sudo edquota -g [groupname]`

* edquota -u -p [userproto] [username] copies userproto's user quota values to username
  * `sudo edquota -u -p [userproto] [username]`

* edquota -g -p [groupproto] [groupname] copies groupproto's group quota values to groupname
  * `sudo edquota -g -p [groupproto] [groupname]`

* edquota -t to set grace periods
  * `sudo edquota -t`

## df: Filesystem usage

The `df` (disk free) utility examines filesystem capacity and usage. In the example below, the -h option means "human-readable" (i.e., in KB, MB, GB, not bytes) and -T shows the filesystem type. Using the -i option would show inode information instead of bytes.

## du: Disk Usage

`du` (disk usage) is used to evaluate both disk capacity and usage.

To display disk usage for the current directory:

* `du`

To list all files, not just directories:

* `du -a`

To list in human-readable format: 

* `du -h`

To display disk usage for a specific directory:

* `du -h somedir`

## Labs
