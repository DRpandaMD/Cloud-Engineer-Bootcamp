# DPKG

[Home](/README.md)

The Debian Package Manager (DPKG) is used by all Debian-based distributions to control the installation, verification, upgrade, and removal of software on Linux systems. The low-level `dpkg` program can perform all these operations, either on just one package, or on a list of packages. Operations which would cause problems (such as removing a package that another package depends on, or installing a package when the system needs other software to be installed first) are blocked from completion.

For Basic Understanding you must know how to:

* Discuss the DPKG packaging system and its uses.

* Explain the naming conventions used for both binary and source deb files.

* Know what source packages look like.

* Use querying and verifying operations on packages.

* Install, upgrade, and uninstall Debian packages.

## DPKG Essentials

* it is pretty much the Debain version of RPM

* not designed to directly retrieve package in day to day use 

* used to install uninstall locally

* the packages have a `.deb` suffix

## Package File Names and Source

* packages come in this format: `<name>_<version>-<revision_number>_<architecture>.deb`
  * `logrotate_3.14.0-4_amd64.deb`
  * `logrotate_3.14.0-4ubuntu3_amd64.deb`

* In debian package system the source contains at least these three files:
  * An upstream tarball, ending with .tar.gz. This is the unmodified source as it comes from the package maintainers.
  * A description file, ending with .dsc, containing the package name and other metadata, such as architecture and dependencies.
  * A second tarball that contains any patches to the upstream source, and additional files created for the package, and ends with a name .debian.tar.gz or .diff.gz, depending on distribution.


## DPKG Queries

List all packages installed:

* `$ dpkg -l`

You can also specify a package name.

List files installed in the wget package:

* `$ dpkg -L wget`

Show information about an installed package:

* `$ dpkg -p wget`

Show information about a package file:

* `$ dpkg -I webfs_1.21+ds1-8_amd64.deb`

List files in a package file:

* `$ dpkg -c webfs_1.21+ds1-8_amd64.deb`

Show what package owns /etc/init/networking.conf:

* `$ dpkg -S /etc/init/networking.conf`

Show the status of a package:

* `$ dpkg -s wget`

Verify the installed package's integrity:

*  `$ dpkg -V package`


## Installing Upgrading Uninstalling Packages with DPKG

* `sudo dpkg -i foobar.deb`
  * It would install or upgrade the foobar package
  * if the package is not installed -- then it would install it
  * if the package is newer than the one currently installed it would upgrade it

* `sudo dpkg -r package` -- removes package *except* config files

* `sudo dpkg -P package` -- will PURGE the package including the config files

## Lab 7.1

* Find out what package the file/etc/logrotate.confbelongs to.
* List information about the package including all the files it contains.
* Verify the package installation.
* Try to remove the package

Commands used:

```bash
dpkg --help
pkg -S /etc/logrotate.conf
dpkg -L logrotate
dpkg -V logrotate
sudo dpkg -r logrotate
```
