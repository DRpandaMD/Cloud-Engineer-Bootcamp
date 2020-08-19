# I/O Scheduling

[Home](/README.md)

Learn this:

* Explain the importance of I/O scheduling and describe the conflicting requirements that need to be satisfied.

* Delineate and contrast the options available under Linux.

* Understand how the CFQ (Completely Fair Queue) and Deadline algorithms work.

## Disk Bottlenecks and I/O Scheduling 

* Both the VM (Virtual Memory) and VFS (Virtual File System) layers submit I/O requests to block devices

* it is the job of the I/O scheduling layer to prioritize and order these requests before they are given to the block devices. 

* There is a need to balance hardware access times, latency, deadlines, fairness, and efficiency.

### I/O Scheduling Algorithm Requirements

* Hardware access times should be minimized; i.e., requests should be ordered according to physical location on the disk. This leads to an elevator scheme where requests are inserted in the pending queue in physical order.

* Requests should be merged to the extent possible to get as big a contiguous region as possible, which also minimizes disk access time.

* Requests should be satisfied with as low a latency as is feasible; indeed, in some cases, determinism (in the sense of deadlines) may be important.

* Write operations can usually wait to migrate from caches to disk without stalling processes. Read operations, however, almost always require a process to wait for completion before proceeding further. 
Favoring reads over writes leads to better parallelism and system responsiveness.

* Processes should share the I/O bandwidth in a fair, or at least consciously prioritized fashion; even if it means some overall performance slowdown of the I/O layer, process throughput should not suffer inordinately.

* *Some of this gets a little cloudy with SSDs*


## I/O Scheduler

* The default is `deadline-mq`

* There is also `bfq`

* and kyber

## I/O and SSDs

* flash devices do not require elevator scheme

* benefits from wear leveling to spread I/O over the devices that have limited write/erase cycles

* You can examine `/sys/block/<device>/queue/rotational` to see whether or not the device is an SSD

## CFQ (Completely Fair Queue) Scheduler

* Additional reading [Cloud Bees Rollout Blog for CFQ](https://rollout.io/blog/linux-io-scheduler-tuning/)

Theoretically, each process has its own I/O queue, which works together with a dispatch queue which receives the actual requests on the way to the device. In actual practice, the number of queues is fixed (at 64) and a hash process based on the process ID is used to select a queue when a request is submitted. 

Dequeuing of requests is done round robin style on all the queues, each one of which works in FIFO (First In First Out) order. Thus, the work is spread out. To avoid excessive seeking operations, an entire round is selected, and then sorted into the dispatch queue before actual I/O requests are issued to the device.

### Tunables

* quantum -- Maximum queue length in one round of service. (Default = 4)

* queued -- Minimum request allocation per queue. (Default = 8)

* fifo_expire_sync -- FIFO timeout for sync requests. (Default = HZ/2)

* fifo_expire_async -- FIFO timeout for async requests. (Default = 5 * HZ)

* fifo_batch_expire -- Rate at which the FIFO's expire. (Default = HZ/8)

* back_seek_max -- Maximum backwards seek, in KB. (Default = 16K)

* back_seek_penalty -- Penalty for a backwards seek. (Default = 2)


## Deadline Scheduler

* The Deadline I/O scheduler aggressively reorders requests with the simultaneous goals of improving overall performance and preventing large latencies for individual request

* With each and every request, the kernel associates a deadline

* Reads get more priority than writes

* Five separate I/O queues are maintained:
  * Two sorted lists are maintained, one for reading and one for writing, and arranged by starting block.​
  * Two FIFO lists are maintained, again one for reading and one for writing. These lists are sorted by submission time.​
  * A fifth queue contains the requests that are to be shoveled to the device driver itself. This is called the dispatch queue.

### Deadline Tunables

* Read_expire -- how long in ms a read request is guaranteed to occur defaulted to HZ/2 = 500

* write_expire --  how long in ms a write request is guaranteed to occur defaulted to 5*HZ = 5000

* writes_starved -- how many request should we give preference to reads over writes defaulted to 2

* fifo_batch --  how many request should be moved from the sorted scheduler list ot eh dispatch queue, when the deadlines have expired defaulted to 16

* front_merges -- back merges ? are more common than front merges as a contiguous request continues to the next block.  Setting this param to 0 disables front merges giving a boost to back merges ...if you know they are unlikely to be needed