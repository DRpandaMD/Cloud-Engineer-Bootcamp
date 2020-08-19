# System Monitoring

[Home](/README.md)

## High Level -- What you need to know

* Understand the concept of inventory and gain familiarity with available system monitoring tools.

* Understand where the system stores log files and examine the most important ones.

* Use the `/proc` and `/sys` pseudo-filesystems.

* Use `sar` to gather system activity and performance data and create reports that are readable by humans.


## log files

* system logs are found in `/var/log`

* important messages are not only sent to log files but to system console window 
  * copies can be found in `/var/log/messages/` OR `/var/log/syslog/` -- ubuntu

* view messages continuously with `sudo tail -f /var/log/messages` or `/var/log/syslog`

* to avoid log files getting out of control (being HUGE) use `logrotate` to manage the logs


## pseudofilesystems /proc and /sys

* only contained in memory

* most of the tunable system parameters can be found in the subdirectory tree `/proc/sys`


## sar

* `sar` stands for Systems Activity Reporter

* it is a tool to gather system activiy and performance data, and creating human readable reports


## Lab 11.1

* see [stress-ng](https://wiki.ubuntu.com/Kernel/Reference/stress-ng)

* try to  install stress-ng from the package manager

* if not install it from source

```bash
git clone git://kernel.ubuntu.com/cking/stress-ng.git
cd stress-ng
make
sudo make install
```

## Quiz 

* Which of the following is a tracing and debugging tool that shows how a process makes requests to the operating system? -- `strace`

* Which of the following is a tool which shows for how long the system is running? -- `uptime`

* Which of the following is an interactive tool for monitoring process activity? -- `top`

* Which of the following is a tool that displays the summary of memory usage? -- `free`