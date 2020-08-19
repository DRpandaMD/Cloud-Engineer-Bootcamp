# APT

[Home](/README.md)

* APT(Advanced Packaging Tool) is the Debian based tool like Yum

* it interfaces with `dpkg`

* for modern systems you can just use `apt` instead of `apt-get`

## Queries

* Queries are done using the apt-cache or apt-file utilities. You may have to install apt-file first, and update its database, as in:
  * `sudo apt install apt-file`
  * `sudo apt-file update`

* Search the repository for a package named apache2:
  `apt-cache search apache2`

* Display basic information about the apache2 package:
  * `apt-cache show apache2`

* Display more detailed information about the apache2 package:
  * `apt-cache showpkg apache2`

* List all dependent packages for apache2:
  * `apt-cache depends apache2`

* Search the repository for a file named apache2.conf:
  * `apt-file search apache2.conf`

* List all files in the apache2 package:
* `apt-file list apache2`


## Installed/Removing/Upgrading Packages with apt

* Synchronize the package index files with their repository sources. The indexes of available packages are fetched from the location(s) specified in `/etc/apt/sources.list`:
  * `sudo apt update`

* Install new packages or update an already installed package:
  * `sudo apt install [package]`

* Remove a package from the system without removing its configuration files:
  * `sudo apt remove [package]`

* Remove a package from the system, as well as its configuration files:
  * `sudo apt --purge remove [package]`

* Apply all available updates to packages already installed:
  * `sudo apt upgrade`

* Do a smart upgrade that will do a more thorough dependency resolution and remove some obsolete packages and install new dependencies:
  * `sudo apt dist-upgrade`
  * *This will not update to a whole new version of the Linux distribution as is commonly misunderstood.*

* *A **Big NOTE** you must update before you upgrade unlike with `yum`*

* Get rid of any packages not needed anymore, such as older Linux kernel versions:
  * `sudo apt autoremove`

* Clean out cache files and any archived package files that have been installed:
  * `sudo apt clean`

## Lab 8.1

Basic APT Commands:

```bash
sudo apt update
sudo apt upgrade
apt-cache search "kernel"
apt-cache search -n "kernel"
sudo apt install apache2-dev
```

## Lab 8.2

Using apt to find information about a package

```bash
apt-cache search bash
apt-cache search -n bash
apt-cache show bash
apt-cache depends bash
apt-cache rdepends bash
```

## Quiz

apt update does not accept a package as argument -- true

`apt-file find <file path>` can't be used to find which package provides the file specified as argument true