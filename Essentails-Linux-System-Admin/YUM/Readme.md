# YUM

[Home](/README.md)

YUM the better way of dealing with packages

* auto resolve dependencies when installing
* updating and removing packages
* external software repos access with synchronization and retrieval of new software as needed

In order to be successful you should know how to do the following:

* Discuss package installers and their characteristics.
* Explain how yum works as a high-level package management system.
* Configure yum to use repositories.
* Discuss the queries yum can be used for.
* Verify, install, remove, and upgrade packages using yum.
* Learn about additional commands and how to install new repositories.
* Understand how to use dnf, which has replaced yum on Fedora.

## Package installers

High level package managers like `yum dnf apt zypper` use databases and software to function much more intelligently than the low level package managers. Here are some examples:

* Can use both local and remote repositories as a source to install and update binary, as well as source software packages.

* Are used to automate the install, upgrade, and removal of software packages.

* Resolve dependencies automatically.

* Save time because there is no need to either download packages manually or search out dependency information separately.

## What is YUM

* `yum` is the front end to `rpm`

* primary task : fetch packages from multiple remote repose and resolve depending among packages

* used in RHEL CentOS Sci-linux and Fedora

* yum uses a cache to speed things up

* to clean up the cache run `yum clean [ packages | metadata | expire-cache | rpmdb | plugins | all ]`

* yum has a number of plugins and companion programs that can be found under `/usr/bin/yum*` and `/usr/sbin/yum*`.

## Queries

* `yum` does what `rpm` does but better -- with the ability to search remote repos

* Search functionality:
  * `sudo yum search keyword` -- gives more info about the packages
  * `sudo yum list "*keyword*"` -- what is installed vs what is available

* Detailed info:
  * `sudo yum info package`

* List packages:
  * `sudo yum list [installed | updates | available]`

* show package groups:
  * `sudo yum grouplist [group1] [group2]`
  * `sudo yum groupinfo group1 [group2]`

* show packages to contain a certain file name
  * `sudo yum /path/to/file`

## Verifying Packages

*package verification requires the installation of `yum-plugin-verify` package!!*

* Run `sudo yum install yum-plugin-verify`

* to verify run
  * `sudo yum verify [package]`

* to mimic  `rpm -V` exactly run
  * `sudo yum verify-rpm [package]`

* to list the diffs, including config files
  * `sudo yum verify-all [package]`

NOTE: *`dnf` does not have an equivalent verify plugin command or option*

## Installing / Removing / Upgrading Packages

* to install and resolve package dependencies
  * `sudo yum install package`  -- like sudo yum install docker

* to install from a local `rpm`
  * `sudo yum localinstall package-file`
  * *note: this is not the same as running the `rpm` version*
  * `yum` will try to resolve dependencies by accessing remote repositories

* to install a software group from repo
  * `sudo yum groupinstall group-name`
  * `sudo yum install @group-name`

* remove package
  * `sudo yum remove package`
  * *note: don't run with -y as it will auto confirm and delete package dependencies to, which may not be what you are trying to do*

## Additional YUM Commands

* list installed plugins
  * `sudo yum list "yum-plugin*"`

* show list of enabled repos
  * `sudo yum repolist`

* start interactive shell to run multiple YUM commands
  * `sudo yum shell [text-file]` -- yum will read in commands from text file

* to download packages and not install them -- they will go into the `/var/cache/yum/ directory
  * `sudo yum install --downloadonly package`

* show history
  * `sudo yum history` -- surprise surprise

## DNF

* `dnf` is supposed to be a replacement for `yum` it will underline `yum` in RHEL8

* you can use `dnf` on Fedora distros and will point a yum command to a dnf equivalent

* [Use the DNF software package manager](https://docs.fedoraproject.org/en-US/quick-docs/dnf/)

* [Link to DNF.io](https://dnf.readthedocs.io/en/latest/)


## Lab 8.1 - yum commands

* Check to see if there are any available updates for your system.
  * `sudo yum update`  OR  -- this is the only one that will actually try to update
  * `sudo yum check-update` OR
  * `sudo yum list updates`

* Update a particular package.
  * `sudo yum update bash`

* List all installed kernel-related packages, and list all installed or available ones.
  * `sudo yum list installed "kernel*"`

* Install the `httpd`package, or anything else you might not have installed yet. Doing a simple: `$ sudo yum list` will let you see a complete list; you may want to give a wildcard argument to narrow the list
  * `sudo yum install httpd -y`


## Lab 8.2 use yum to dig up information about a package

Using yum

* find all packages that contain a reference to `bash`
  * `sudo yum search bash`

* find all installed available packages for `bash`
  * `sudo yum list bash`

* find the package information for `bash`
  * `sudo yum info bash`

* find the dependencies for the `bash` package
  * `sudo yum deplist bash`

## Lab 8.3 Managing groups of packages with yum

* Use the following command to list all package groups available on your system:
  * `$ sudo yum grouplist`

* Identify theBackup `Clientgroup` and generate the information about this group using the command
  * `$ sudo yum groupinfo "Backup Client"`

* Install using:
  * `$ sudo yum groupinstall "Backup Client"`

* Identify  a  package  group  that’s  currently  installed  on  your  system  and  that  you  don’t  need.   Remove  it  `usingyumgroupremoveas` in:
  * `$ sudo yum groupremove "Backup Client"`
  
*Note you will be prompted to confirm removal so you can safely type the command to see how it works.You may find that `thegroupremove` does **not** remove everything that was installed; whether this is a bug or a feature can be discussed.*


## Lab 8.4 add new yum repo

* Create a new repository file called `webmin.repoin the/etc/yum.repos.d`directory. It should contain the following:

* see [webmin download](http://www.webmin.com/download.html)

* see [webmin on sourceforge](https://sourceforge.net/projects/webadmin/)

```bash
[Webmin]
name=Webmin
Distribution Neutral
baseurl=http://download.webmin.com/download/yum
mirrorlist=http://download.webmin.com/download/yum/mirrorlist
enabled=1
gpgcheck=0
```