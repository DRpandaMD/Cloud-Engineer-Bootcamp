# Package Management Systems

[Home](/README.md)

## Software Packaging Concepts

Package managers are not a new concept to me but here are some basics about them:

* Gather and compress associated software files into a single package (archive), which may require one or more other packages to be installed first.

* Allow for easy software installation or removal.​

* Can verify file integrity via an internal database.​

* Can authenticate the origin of packages.​

* Facilitate upgrades.​

* Group packages by logical features.​

* Manage dependencies between packages.

## Why use packages

* Automation: no manual install and upgrades

* scalability: install on one or many systems, through scripting and automation

* repeatability and predictability

* security and auditing

## Package Types

### Binary Packages

* contain files ready for deployment
  * executables
  * libraries

* architecture-dependent and must be compiled for each type of machine

* dealt with by SAs most of the time

### source packages

* used to generate binary packages

* should always be able to rebuild a binary package 
  * by using `rpmbuild -- rebuild`

* you can use the source packages to better track changes and the source code

* example:
  * `rpmbuild --rebuild -rb p7zip-16.02-16.e18.src.rpm`
  * will place the result in `/root/rpmbuild`

## Common Package Mangers

* the two most common types of package managers are **RPM** (Red Hat Package Manager) 

* and **dpkg** (debian package) or *APT*

## Packaging Tools Levels and Varieties

### Low Level Utilities

* used for single package or list of packages 

* no dependency tracking

* if another package needs to be installed first, installation will fail

* if the package is need by another package, removal will fail

* `rpm` and `dpkg` are of this type

### High Level Utilities

* solves the dependency problem

* if another package or group of packages need to be installed before software can be installed, such needs will be satisfied

* if removing a package interferes with another installed package, the admisistrator will be given the choice of either aborting, or removing all affected software

* on RPM systems you can use `yum` `dnf` or `zypper`

* on dpkg systems you can use `apt` and `apt-cache`

## Creating Software Packages

You can create your own `.deb` or `.rpm` packages.  You would need to do this to have software you want to install or set up certain things like:

* Creating needed symbolic links

* Creating directories as needed

* Setting permissions

* Anything that can be scripted.

## Version control

They give a HUGE list of different types "Revision Control Systems"

* I will tell you the only thing you should worry about is **git**

* for remotes, obviously github and gitlab.

* bitbucket is also out there as well as every major public cloud has one too

* [Git Man Page](https://mirrors.edge.kernel.org/pub/software/scm/git/docs/)

## Git

* they have a section in here about it

* I am not adding it, you should already know what git is its 2020 FFS

* also I need GitOps in my life

## Lab 5.1 Version Control with git

### Commands I used

```bash
mkdir git-test && cd git-test
git init
ls -l
ls -l .git
echo some junk> junkfile.txt
git add -A
git status
git config user.name "bob"
git config user.email "bob@thebuilder.net"
cat junkfile.txt
echo some more junk >> junkfile.txt
cat junkfile.txt
git diff
git commit -m "my initial commit"
git log
git log --all --decorate --oneline --graph
```
