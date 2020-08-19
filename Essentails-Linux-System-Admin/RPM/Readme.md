# RPM

[Home](/README.md)

## Things you want to understand how to do

* Understand how the RPM system is organized and what major operations the rpm program can accomplish.

* Explain the naming conventions used for both binary and source rpm files.

* Query, verify, install, uninstall, upgrade and freshen packages.

* Grasp why new kernels should be installed, rather than upgraded.

* Use `rpm2cpio` to copy packaged files into a `cpio` archive, as well as to extract the files without installing them.

## Advantages of using RPM

* All files related to a specific task or a subsystem are packaged into a single file, which also contains information about how and where to install and uninstall the files.

* when a new version of a program is released, a new RPM package is usually released too

* these files may not work for other Linux Distros

* Unless RPM is given a URL, it will not by itself install anything over the network

* RPM will default to using the local machine using absolute or relative paths

### For System Admins

* RPM makes it easier to manage software packages

* it makes easy to pick out versions and whether it was installed correctly

* it also makes it easy to uninstall packages

### For developers

* RPM keeps the builders for its target distro separate from the Source Code

* thus it easier to maintain the changes in to code

* additionally since all the build information is located in one place

* facilitates building for different versions Linux for different cpu architectures

## Package File Names

* There is a `rpm` standard [here](https://rpm.org/)

* The standard naming format for a binary package is:

`<name>-<version>-<release>.<distro>.<architecture>.rpm`

`sed-4.5-1.e18.x86_64`

* The standard naming format for a source package is:

`<name>-<version>-<release>.<distro>.src.rpm`

`sed-4.5-1.e18.src.rpm`

## RPM Database and Helper Programs

* the `rpm` database files are in `/var/lib/rpm`

* they are in the form of "Berkeley DB hash" files

* the data base files should not be changed manually

* instead just rely on the `rpm` tool

* you can make an alternate database directory with the `--dbpath` option

* you can also use the `--rebuilddb` option to rebuild the database indices from the installed package headers

* Helper Programs live in `/usr/lib/rpm`

## Queries

Which version of a package is installed?

* `$ rpm -q bash`

Which package did this file come from?

* `$ rpm -qf /bin/bash`

What files were installed by this package?

* `$ rpm -ql bash`

Show information about this package.

* `$ rpm -qi bash`

Show information about this package from the package file, not the package database.

* `$ rpm -qip foo-1.0.0-1.noarch.rpm`

List all installed packages on this system.

* `$ rpm -qa`

A couple of other useful options are `--requires` and `--whatprovides`.

The `--requires` option will return a list of prerequisites for a package:

* `$ rpm -qp --requires foo-1.0.0-1.noarch.rpm`

The `--whatprovides` option will show what installed package provides a particular requisite package:

* `$ rpm -q --whatprovides libc.so.6`


## Verifying Package

Use the `-v` option to verify files in a package are consistent to what is in the system `rpm` DB

To Verify all packages on a system run:

* `rpm -Va`

* Some of the outputs you will see have meaning:
  * S: file size differs
  * M: file permissions and/or type differs
  * 5: MD5 checksum differs
  * D: device major/minor number mismatch
  * L: symbolic link path mismatch
  * U: user ownership differs
  * G: group ownership differs
  * T: modification time differs
  * P: capabilities differ.

## Installing packages

Run:

* `sudo rpm -ivh {packageName}`

* `-i` == install; `-v` == verbose; `-h` == print out hash marks to show progress

### Tasks for RPM

* Performs dependency checks

* Performs Conflict checks

* executes commands required before installation

* Deals with configurations files

* Unpacks files from packages and installs them with correct attributes

* Executes commands required after installations

* updates the system RPM database

## Uninstalling packages

To uninstall a package run  the `-e` option (erase)

* `sudo rpm -e system-config-lvm` as an example

* It will return a error message saying *x* package is not there when you try to delete a package

* also it will throw an error message about dependencies when you try to remove *x* package that *y* package will need to run

* You can use the `--test` option to dry run and see if the *erase* will work

* *Never erase the rpm package itself, you will have to re-install the OS if you do*

## Updating Packages

To update you will use the `-U` flag

* `rpm -Uvh {packageName}`

* if you try to use the `-U` flag and the package is not there it will simply just install the package

* you can't use the `-i` flag as it tries to overwrite existing files

* with the upgrade it writes the *NEW* files then deletes the old ones

* you can also *downgrade* by using the `-U` flag and `--oldpackage` option

## Freshening Packages

Useful for when you are applying patches

* `sudo rpm -Fvh *.rpm` -- will attempt to freshen the package in this current directory
  * If an older version of a package is installed, it will be upgraded to the newer version in the directory.
  * If the version on the system is the same as the one in the directory, nothing happens.
  * If there is no version of a package installed, the package in the directory is ignored.

## Upgrading the Linux Kernel

**Important:** *If an older version of a package is installed, it will be upgraded to the newer version in the directory. If the version on the system is the same as the one in the directory, nothing happens.If there is no version of a package installed, the package in the directory is ignored.*

If you have problems after the reboot you will not be able to boot into the old kernel

BUT, you can `-i` and install both kernels and you can pick which one to boot into

To install a new kernel on a RHEL based system:

* `sudo rpm -ivh kernel-{version}.{arch}.rpm`


## Using rpm2cpio

For the times you want to archive a rpm package

* `rpm2cpi` will convert a package to a `cpio` archive

* you can also extract files, just keep in mind it will happen in the current directory


## Lab 6.1

1. Find out what package the file/etc/logrotate.confbelongs to.

2. List information about the package including all the files it contains.

3. Verify the package installation.

4. Try to remove the package

commands used

```bash
rpm -qf /etc/logrotate.conf
rpm -qil logrotate
rpm -qil $(rpm -qf /etc/logrotate.conf)
rpm -V logrotate
sudo rpm -e logrotate
```

## Lab 6.2

reminder to redo