# Memory Monitoring

[Home](/README.md)

Learn this:

* List the primary (inter-related) considerations and tasks involved in memory tuning.

* Use entries in `/proc/sys/vm`, and decipher `/proc/meminfo`.

* Use ``vmstat`` to display information about memory, paging, I/O, processor activity, and processes' memory consumption.

* Understand how the OOM-killer decides when to take action and selects which processes should be exterminated to open up some memory.

## Memory Monitoring tweaks

When tweaking parameters in `/proc/sys/vm`, the usual best practice is to adjust one thing at a time and look for effects. The primary (inter-related) tasks are:

* Controlling flushing parameters; i.e., how many pages are allowed to be dirty and how often they are flushed out to disk.

* Controlling swap behavior; i.e., how much pages that reflect file contents are allowed to remain in memory, as opposed to those that need to be swapped out as they have no other backing store.

* Controlling how much memory overcommission is allowed, since many programs never need the full amount of memory they request, particularly because of copy on write (COW) techniques.

### Table for memory tools

| UTILITY   | PURPOSE   | PACKAGE |
| --------- | --------- | -------- |
| free  |  Brief summary of memory usage   | procps |
| vmstat  |  Detailed virtual memory statistics and block I/O, dynamically updated   | procps |
| pmap  |  Process memory map  |  procps |


## /proc/meminfo

* remember that /proc/ is a pseudo file system

* /proc/meminfo/ has a TON of info

### Table of meminfo entries

| ENTRY |   MEANING |
| ------ | ------- |
| MemTotal  |   Total usable RAM (physical minus some kernel reserved memory) |
| MemFree  |   Free memory in both low and high zones |
| Buffers  |   Memory used for temporary block I/O storage |
| Cached   |  Page cache memory, mostly for file I/O |
| SwapCached |    Memory that was swapped back in but is still in the swap file |
| Active   |  Recently used memory, not to be claimed first |
| Inactive  |   Memory not recently used, more eligible for reclamation |
| Active(anon)   |  Active memory for anonymous pages |
| Inactive(anon)   |  Inactive memory for anonymous pages |
| Active(file)   |  Active memory for file-backed pages |
| Inactive(file) |    Inactive memory for file-backed pages |
| Unevictable  |   Pages which can not be swapped out of memory or released |
| Mlocked    | Pages which are locked in memory |
| SwapTotal  |   Total swap space available |
| SwapFree  |   Swap space not being used |
| Dirty   |  Memory which needs to be written back to disk |
| Writeback  |   Memory actively being written back to disk |
| AnonPages  |   Non-file back pages in cache |
| Mapped  |   Memory mapped pages, such as libraries |
| Shmem  |   Pages used for shared memory |
| Slab  |   Memory used in slabs |
| SReclaimable |    Cached memory in slabs that can be reclaimed |
| SUnreclaim   |  Memory in slabs that can't be reclaimed |
| KernelStack  |   Memory used in kernel stack |
| PageTables |    Memory being used by page table structures |
| Bounce  |   Memory used for block device bounce buffers |
| WritebackTmp  |   Memory used by FUSE filesystems for writeback buffers |
| CommitLimit   |  Total memory available to be used, including overcommission |
| Committed_AS  |   Total memory presently allocated, whether or not it is used |
| VmallocTotal  |   Total memory available in kernel for vmalloc allocations |
| VmallocUsed  |   Memory actually used by vmalloc allocations |
| VmallocChunk  |   Largest possible contiguous vmalloc area |
| HugePages_Total  |   Total size of the huge page pool |
| HugePages_Free  |   Huge pages that are not yet allocated |
| HugePages_Rsvd  |   Huge pages that have been reserved, but not yet used |
| HugePages_Surp  |   Huge pages that are surplus, used for overcommission |
| Hugepagesize  |   Size of a huge page |


## /proc/sys/vm

Here is where you can tune the Virtual Memory systems

* you can make changes by writing to the entry

* or by `sysctl`

* or by modifying `/etc/sysctl.conf` values at boot time

## vmstat

* see [vmstat man page](https://linux.die.net/man/8/vmstat)

`vmstat` is a multi-purpose tool that displays information about memory, paging, I/O, processor activity and processes. It has many options. The general form of the command is:

`vmstat [options] [delay] [count]`

If delay is given in seconds, the report is repeated at that interval count times; if count is not given, `vmstat` will keep reporting statistics forever, until it is killed by a signal, such as Ctrl-C.

If no other arguments are given, you can see what vmstat displays, where the first line shows averages since the last reboot, while succeeding lines show activity during the specified interval.

## OOM Killer

the kernel permits overcommission of memory, but only for pages dedicated to user processes; pages used within the kernel are not swappable and are always allocated at request time.

You can modify, and even turn off this overcommission by setting the value of `/proc/sys/vm/overcommit_memory`:

0. (default) Permit overcommission, but refuse obvious overcommits, and give root users somewhat more memory allocation than normal users.

1. All memory requests are allowed to overcommit.

2. Turn off overcommission. Memory requests will fail when the total memory commit reaches the size of the swap space plus a configurable percentage (50 by default) of RAM. This factor is modified changing `/proc/sys/vm/overcommit_ratio`.

If available memory is exhausted, Linux invokes the OOM-killer (Out Of Memory) to decide which process(es) should be exterminated to open up some memory.

There is no precise science for this; the algorithm must be heuristic and cannot satisfy everyone. In the minds of many developers, the purpose of the OOM-killer is to permit a graceful shutdown, rather than be a part of normal operations.
