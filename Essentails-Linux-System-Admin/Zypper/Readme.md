# Zypper

[Home](/README.md)

* Zypper is the yum for SUSE based linux distros

* The approach is the same here as with the other package managers

## zypper Queries

* show list of updates
  * `zypper list-updates`

* list available repos
  * `zypper repos`

* search repos for a string
  * `zypper search <a string>`

* list info about a package
  * `zypper info firefox`

* search repo to see what packages provide a file
  * `zypper search --provides /usr/bin/firefox`


## installing / removing / upgrading packages with zypper

* Install or update package(s):
  * `sudo zypper install firefox`

* Do not ask for confirmation when installing or upgrading:
  * `sudo zypper --non-interactive install firefox`
  * *This is useful for scripts and is equivalent to running yum -y.*

* Update all installed packages:
  * `sudo zypper update`

* Giving package names as an argument will update only those packages and any required dependencies. Do this without asking for confirmation:
  * `sudo zypper --non-interactive update`

* Remove a package from the system:
  `sudo zypper remove firefox`
  *Like with yum, you have to be careful with the removal command, as any package that needs the package being removed would be removed as well.*


## additional zypper commands

* Sometimes, a number of zypper commands must be run in a sequence. To avoid re-reading all the databases for each command, you can run zypper in shell mode, as in:

``` bash
sudo zypper shell
> install bash
...
> exit
```

* *Because zypper supports the readline library, you can use all the same command line editing functions in the zypper shell available in the bash shell.*

* To add a new repository:
  * `sudo zypper addrepo URI alias`
  * *which is located at the supplied URI and will use the supplied alias.*

* To remove a repository from the list:
  * `sudo zypper removerepo alias`
  * *using the alias of the repository you want to delete.*

* To clean up and save space in `/var/cache/zypp`
  * `sudo zypper clean [--all]`

## Lab 9.1

Revisit as needed

SUSE / sesl seemed not to work to well on GCP