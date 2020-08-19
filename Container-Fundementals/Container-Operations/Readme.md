# Container Operations Operations

* [Home](/README.md)

## What to learn

* Understand containers and container operations in general.

* Explain different operations supported by the container runtimes: runc, Docker, rkt, CRI-O, and podman.

* Perform container operations with runc, Docker, rkt, and podman.

## Containers

* A container is a process running on the host system

* this process is started by the container runtime from a container image, and the runtime provides a set of tools to manage the container process

* The container is an isolated environment that encapsulates a running application, and it runs based on the configuration defined in the container image

* virtualization features available at the kernel level are attached to the container process to help manage various aspects of the container’s virtual environment

* *Namespaces* virtualize the container process’s PID, network, root, and users.

* *Cgroups* help set resource usage limits the container process can consume on the host system, and security contexts enforce permissions the container process has on the host system

## Container Operations

Check the Docs.  NO seriously go check them out.  The linux foundation goes over them, but the source documentation is better.

## Labs

The docker one might be worth doing.


## Quiz

Which of the following docker and podman commands can be used to display detailed information about a container?

* docker container inspect

* podman container inspect

How can we create a new image from a container or Pod?

The wording for this quiz question is terrible, from Docker 

*it can be useful to commit a container’s file changes or settings into a new image. This allows you to debug a container by running an interactive shell, or to export a working dataset to another server. Generally, it is better to use Dockerfiles to manage your images in a documented and maintainable way.*

Example: `docker commit c3f279d17e0a  svendowideit/testimage:version3`