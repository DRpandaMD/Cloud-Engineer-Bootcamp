# Process Monitoring

[Home](/README.md)

Keeping track of running (and sleeping) processes is an essential system administration task. The `ps` program has been a main tool for doing so in UNIX-based operating systems for decades. 

Another trusty tool is provided by `top`, which interactively monitors the system's state.

Key take aways:

* Use `ps` to view characteristics and statistics associated with processes.

* Identify different `ps` output fields and customize the `ps` output.

* Use `pstree` to get a visual description of the process ancestry and multi-threaded applications.

* Use `top` to view system loads interactively.

## Process Monitoring Tools

| Utility  | Purpose | Package |
| ------- | -------- | ------- |
| `top`    | Process activity, dynamically updated     | `procps` |
| `uptime` |     How long the system is running and the average load    | `procps` |
| `ps` |    Detailed information about processes |    `procps` |
| `pstree` |    A tree of processes and their connections     |`psmisc` (or `pstree`) |
| `mpstat` |    Multiple processor usage |     `sysstat` |
| `iostat` |    CPU utilization and I/O statistics |    `sysstat` |
| `sar` |    Display and collect information about system activity |    `sysstat` |
| `numastat` |    Information about NUMA (Non-Uniform Memory Architecture) |    `numactl` |
| `strace` |     Information about all system calls a process makes     |`strace` |


## Viewing Process States with ps


`ps` is a workhorse for displaying characteristics and statistics associated with processes, all of which are garnered from the /proc directory associated with the process.

Some common choices of options are:

* `ps aux`

* `ps -elf`

* `ps -eL`

* `ps -C "bash"`

## Customizing the ps Output

If you use the -o option, followed by a comma-separated list of field identifiers, you can print out a customized list of ps fields:

* `pid`: Process ID number

* `uid`: User ID number

* `cmd`: Command with all arguments

* `cputime`: Cumulative CPU time

* `pmem`: Ratio of the process's resident set size to the physical memory on the machine, expressed as a percentage.


## Using pstree

`pstree` gives a visual description of the process ancestry and multi-threaded applications


## Top 

When you want to know what the system is spending its time on, the first tool you often use is `top`. The screenshot shows you what you can see when using `top` without arguments. By default, `top` refreshes itself every 3.0 seconds.

`top` is an ancient utility and has a ton of options, as well as interactive commands triggered when certain keys are pressed. For example, if you hit 1, each CPU is shown separately, and if you hit i, only active processes are shown. You can see what doing both gives you in the screenshot.

## Lab 12.1 

1. Run `ps` with the options-ef. Then run it again with the options aux. Note the differences in the output.

2. Run `ps` so that only the process ID, priority, `nice` value, and the process command line are displayed.

3. Start a new `bash` session by typing `bash` at the command line.  Start another `bash` session using the `nice` command but this time giving it a `nice` value of10.

4. Run `ps` as in step 2 to note the differences in priority and `nice` values. Note the process ID of the two `bash` sessions.

5. Change the `nice` value of one of the `bash` sessions to 15 using `renice`. Once again, observe the change in priority and `nice` values.

6. Run top and watch the output as it changes. Hit `q` to stop the program.

* *This one was really weird, actually all these labs are very strange.  I am just entering commands because they want me too*

## Lab 12.2

1. Use `dd` to start a background process which reads from `/dev/urandom` and writes to `/dev/null`.

2. Check the process state. What should it be?

3. Bring the process to the foreground using the `fg` command.  Then hitCtrl-Z. What does this do?  Look at the process state again, what is it?

4. Run the jobs program. What does it tell you?

5. Bring the job back to the foreground, then terminate it using kill from another window