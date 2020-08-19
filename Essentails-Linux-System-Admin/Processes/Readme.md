# Processes

## Objectives

* Describe a process and the resources associated with it.

* Describe the role of the init process.

* Distinguish between processes, programs and threads.

* Understand process attributes, permissions and states, and know how to control limits.

* Explain the difference between running in user and kernel modes.

* Describe daemon processes.

* Understand how new processes are forked (created).

* Use nice and renice to set and modify priorities.

* Understand how shared and static libraries are used.

## Programs, Processes and Threads

* a *program* is a set of instructions, along with any internal data used while carrying the instructions out. Could have internal or external data (or both)

* a *process* is an executing program and associated resources including environment open files and signal handlers. A program could be executing more than once simultaneously -- it is then responsible for multiple process.

* a **multi-threaded process** occurs when two or more task (threads of execution ) at the same time, can share various resources: like memory space, open files or a queue.

* there is mention about how there are **heavy weight** and **light weight** processes in other operation systems.  

* For linux each thread of execution is considered individually

* Linux does a much better job of context switching and creation / destruction of processes

* This means that a multi threaded app behaves much more like spawning off multiple processes.

## What is a process

* A process is an instance of a program in execution. The process may be in a number of states like running or sleeping

* Every process has :
  * `pid` -- Process ID
  * `ppid` -- Parent Process ID
  * `pgid` --Process Group ID

* Every process has the following resources :
  * program code
  * data
  * variables
  * file descriptors
  * an environment

* `init` is usually the first process to run on a system and become the ancestor of all subsequent process running on the system.

* Except for those initiated from the kernel.  Those show up with a `[]` around the name in the `ps` listing

* it is important to note that a process can spawn off children process making a *child* / *parent* relationship

* The *child* process can die early making them *zombie* processes

* part of `init`s job is clean them up often referred to a *zombie killer* or *child reaper*

* Processes are controlled by *scheduling* which is complete preemptive. only the kernel has the right to preempt a process; they cannot do it to each other

```bash
There actually is a whole lot more detail not told here about preemptive cpu architecture
and OS design.  Once upon a time you could not preempt a process in execution you would have to wait for it to be finished.
```

## Process Attributes

* All Process have these attributes:
  * The program being executed
  * Context (state)
  * Permissions
  * Associated resources.

* *contex* : when ever a process gets preempted ( another process is about to take cpu cycles), the process will take a snapshot of its state.  This will include the state of the cpu registers (again not explained, but in the registers there will be instruction and machine instructions and what is the data IN the registers. [MUL, F1, F2, L3 { Multiple whats in F1 and F2 and put it L3}.  While the new process is loading up, out of the cache the cpu will "flush" with some 0s to clean out whats left over in the registers  THIS complex: copy to l1 cache, flush, load instructions and execution is what makes context switching so important])

* Processes can be scheduled in and out when sharing CPU time with others, or be put to sleep waiting on I/O or user response.  Thus being able to store the context, load up a new set of process instruction, then swap back to the original to continue execution is critical to the kernels ability to do context switching

* "Every process has permissions based on which user has called it to execute. It may also have permissions based on who owns its program file. Programs which are marked with an ”s” execute bit have a different ”effective” user id than their ”real” user id. These programs are referred to as `setuid` programs.

* *NOTE* `setuid` programs owned be an security problem

* As an example: the `passwd` program is an example of a `setuid` program

## Process Resource Isolation

* processes are isolated in its own user space, with its own spot in memory

* creates greater stability and promotes security

* process do not have direct access to hardware

* they must rely on **System calls** to interact with the hardware

* this goes back to scheduling and context switching.  

## Controlling Processes with ulimit

* `ulimit` is a built-in bash command that displays or resets a number of resource limits associated with processes running under a shell.

* A system administrator may need to change some of these values in either direction:
  * To restrict capabilities so an individual user and/or process cannot exhaust system resources, such as memory, cpu time or the maximum number of processes on the system.
  * To expand capabilities so a process does not run into resource limits; for example, a server handling many clients may find that the default of 1024 open files makes its work impossible to perform.


## Process states

* Running
  * executing or sitting in the **run queue** 

* Sleeping --- waiting, usually on I/O

* Stopped
  * process has been suspended
  * usually happens when a programmer wants to examine the executing programs memory, CPU registers, flags etc
  * usually done by a debugger

* Zombie
  * also called a **defunct** process 
  * happens when it terminates and no other process as asked about the exit state 
  * A zombie process has released all of its resources except exit state and its entry in the process table.
  * if the parent of any process dies, it is adopted by `init` PID=1 or `kthreadd` PID=2

## Execution Modes

### User Mode

* except when executing a system call, process execute in user mode where they have lesser privileges than in kernel mode

* this will follow the guidelines of **process resource isolation**

### System Mode

* only the system itself runs here 

* no application may ever live here

* even processes start off as `root` go to user mode

* any type of system managed hardware that an app wants to use must do so through a system call


## Daemons

A daemon process is a background process whose sole purpose is to provide some specific service to users of the system:

* Daemons can be quite efficient because they only operate when needed.
* Many daemons are started at boot time.
* Daemon names often (but not always) end with d.
* Some examples include `httpd` and `systemd-udevd`.
* Daemons may respond to external events (`systemd-udevd`) or elapsed time (`crond`).
* Daemons generally have no controlling terminal and no standard input/output devices.
* Daemons sometimes provide better security control.


## Creating Processes

* An average Linux system is always creating new processes. This is often called `forking`; the original parent process keeps running, while the new child process starts.

* Often, rather than just a fork, one follows it with an `exec`, where the parent process terminates, and the child process inherits the process ID of the parent. The term `fork` and `exec` is used so often, people think of it sometimes as one word.

## Creating Processes in a Command Shell

* A new process is created (forked from the user's login shell).

* A wait system call puts the parent shell process to sleep.

* The command is loaded onto the child process's space via the exec system call. In other words, the code for the command replaces the bash program in the child process's memory space.
* The command completes executing, and the child process dies via the exit system call.
* The parent shell is re-awakened by the death of the child process and proceeds to issue a new shell prompt. 

* The parent shell then waits for the next command request from the user, at which time the cycle will be repeated.

* **background processing** is done by using the `&` at the end of the command

* `tar -czf home.tar.gz . &` will not show the verbiage of the command and dump it into the background

* helpful if you already know the command will work and done want to dump a bunch of gargabe onto the screen

* Some shell commands (such as `echo` and `kill`) are built into the shell itself, and do not involve loading of program files. For these commands, no fork or exec are issued for the execution.

## Using nice to set priorities

* process priority can be controlled with `nice` and `renice`

* niceness (cute) can range -20 (the highest) to +19 the lowest

* `renice` is used to change the nice value on the fly

* only root can *decrease* niceness

## Static and Shared Libraries

* **Static** -- the code for the library function is inserted in the program at compile time and does not change thereafter even if the library is updated

* **Shared** -- The cod for the library functions is loaded into the pgram at run time and if the library is changed later, the program runs withy the new library modifications

* shared libraries are more efficient because they can be used by many apps at once

* shared libraries are also called Dynamic LInk Libraries (DLLs)

### Shared Library Versions

* when there are major changes to Libraries (think Python 2.7 -> Python 3.0) It can break a ton of things

* you can have a program have any specific Major Version 

* most programs will just use `latest`
  * My own thoughts on this your SDLC and delivery pipelines should be robust enough to handle migrating a code base from one major version to another.
  * Also using older versions is just asking for security problems
  * there are people still using Windows 7 and I am just like but why???

* Shared libraries have the extension `.so.` a full version of this would look like:
  * `libc.so.N` where `N` is the major version number

### Finding Shared Libraries

* `ldd` can be used to ascertain what shared libraries an app needs.

* `ldd` will show the `soname` of the library and where it points too

* as an example `ldd /usr/bin/vi` will dump out where the libraries are and where they happen to be in memory

* `ldconfig` is generally run a boot time (but can be run anytime)\

* it uses `/etc/ld.so.conf` which list the directories that will be searched for shared libraries

* `ldconfig` must be run as root and shared libraries should only be stored in system directories when they are stable and useful


## Lab 3.1 Controlling Processes with ulimit

### Commands

bash commands used

```bash
help ulimit
ulimit -n
ulimit -S -n
ulimit -H -n
ulimit -n hard
ulimit -n
clear
ulimit -n 2048
ulimit -n
ulimit -n 4096
sudo ulimit -n 4096
sudo su
ulimit -n
history
```

I honestly Have no idea why this is important other than just controlling a process with limits. My only guess is that you have a host that is a webserver and you don't want the web server to completely dominate the host.

But if that is the case you'd realistically just scale out and put a Layer 7 Load Balancer in front of your hosts

## Lab 3.2: Examining System V IPC Activity

* `System V IPC` is a rather old method of Inter Process Communication that dates back to the early days of UNIX.

* It involves three mechanisms:
  * Shared Memory Segments
  * Semaphores
  * Message Queues

* More modern programs tend to use `POSIX IPC` methods for all three of these mechanisms, but there are still plenty of `SystemV IPC` applications found in the wild

`ipcs`  returned nothing in my ubuntu 18 lts GCE vm.