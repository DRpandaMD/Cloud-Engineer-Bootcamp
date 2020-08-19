# Container Standards and Runtimes 

* [Home](/README.md)

## Key Points

* Discuss containers.
* Understand container image standards.
* Understand container runtimes standards.
* Discuss container runtimes: runc, containerd, Docker, rkt, and CRI-O.
* Describe other runtimes: LXC, LXD, Kata Containers, gVisor, and Podman


## Containers

* to ease the understanding of the concept, a container is compared to a regular Virtual Machine - which is a result of full virtualization

* but in actuality, is the product of several OS-level virtualization features of the Linux kernel used in conjunction to build a lightweight isolated environment

* containers use the host kernel and are bound to boot the host OS only

### What is a Container

A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.  From [Docker.com What is a Container?](https://www.docker.com/resources/what-container)


### Where are containers Deployed??

On a non container app, you'd have to deciede where to put your app, on Baremetal in a data center or on a VM in the cloud using something like VMWare (yuck) or a VM in the cloud.

For a container you don't really have this problem as containers pretty much run on anything

## Standards 

Interoperability has not always been one of the main characteristics of containers. Having several projects implementing similar virtualization features, with each project tackling issues independently in its own way, resulted in an even larger number of guidelines and rules 

As a result, gradually, sets of well defined standards have been put in place to guide not only the building process of container images, but also the configuration of the container runtime environments

Although the community’s goal is to establish a single, unified standard to govern container operations across all platforms and runtimes, there are still two separate standards in use today - the **App Container standard** and the **Open Container Initiative**

### App Container (appc)

the App Container (appc) [appc/spec github repo](https://github.com/appc/spec) specification was introduced in 2014 by CoreOS in collaboration with Google and RedHat. One of the container runtimes implementing the appc specification is rkt. The appc specification defines a container image format, how an application is packaged into a container image, a deployment mechanism and a runtime.

the appc enables the user community to develop tools to build, validate, and convert container images to ACI image format, such as goaci, docker2aci, deb2aci, actool, acbuild, and oci2aci

#### Aspects of the Application Container

##### The App Container Image ACI 

* File system layout of the container image with its directory tree.
* Format of the container image archive, together with compression and encryption options.
* Unique image ID that allows its integrity verification, where the image ID is obtained by hashing the uncompressed ACI archive file.
* Image manifest file which describes the image and includes attributes such as the container name, specification version, users, exec command, file system, environment, resources, mount points, ports, dependencies, and metadata.


##### The App Container Image Discovery

In order to address different URL types such as image, signature and public key, the specification describes the following steps in the ACI discovery process:

* Discovery templates to render images and signature URLs,
* Discovery URL templates to include an image or signature URL together with a URL to the keys required for ACI signature validation,
* Validation rules for ACI signature (optional),
* Authentication when retrieving container images (optional).


##### App Container Pod

A Pod represents an execution context obtained by namespacing various aspects and resources of the Linux Operating System:

* PID namespace where app containers can talk to each other,
* Network namespace where an IP address is shared between app containers,
* IPC namespace for app containers communication,
* UTS namespace for app containers to share a hostname.


##### App Container Executor

Part of the app’s environment, ACE must set the following:

* UUID of Pod for unique identification within a domain,
* File System setup as a chrooted environment for each app container part of the Pod,
* Volume setup and mounted at specified mount points inside the apps,
* Network setup with a loopback interface and optional IP layer interfaces,
* Logging of apps is expected to stdout and stderr, which then ACE is able to capture and persist.


The app’s interaction with the environment is defined by specifying:

* Execution environment such as PATH, app name from the image manifest, metadata service URL and container name,
* Linux isolators specific for seccomp, SELinux, and AppArmor define app permissions and capabilities,
* Resource Isolators to enforce resource constraints for CPU and RAM at Pod level, at individual app level, or a mix of both.

### Open Container Initiative (OCI)

[Open Container Initiative](https://opencontainers.org/) was introduced in 2015 by Docker together with other leaders in the container industry. One of the container runtimes implementing the OCI specification is runC


#### OCI Specifications 

The OCI has two Runtime Specification and Image Specification

* [OCI Image Format Specification](https://github.com/opencontainers/image-spec#oci-image-format-specification)

* [OCI Runtime Specification](https://github.com/opencontainers/runtime-spec#open-container-initiative-runtime-specification)


## Container Runtimes

The role of a container runtime is to provide an environment supporting basic operations with images and the running containers, that is both configurable and consistent, where container processes are able to run

### runc

[runc](https://github.com/opencontainers/runc#runc) is a CLI tool for spawning and running containers according to the OCI spec

Its simplicity, however, is not without shortcomings. runc does not expose an API and does not provide container image management capabilities. While it does not support image build operations, it does not provide image download or image integrity check capabilities either.

runc does not include a centralized daemon, it may be integrated with the Linux service manager - systemd

### containerd

[containerd](https://containerd.io/) is An industry-standard container runtime with an emphasis on simplicity, robustness and portability.  *also recently graduated within the CNCF*

As an industry-standard container runtime, containerd was designed to run as an embedded daemon of a more robust container management system, and not to be used directly by everyday users. Among the adopters of the containerd daemon are the Docker engine, Kubernetes services of IBM (IKS) and Google Cloud (GKE), Cloud Foundry, and Kata Containers.

containerd supports the OCI container image specification and the OCI runtime specification by utilizing runc as its low level OCI runtime with the possibility to extend it with plugins to support the Kubernetes’ Container Runtime Interface (CRI) as well

### Docker

Who hasn't heard of [Docker](https://www.docker.com/)


Earlier Docker versions were based on the Linux Containers (LXC) container runtime as its low level runtime, which came with a few limitations at that time. As a result, Docker decided to develop in-house its own proprietary runtime, libcontainer, to replace LXC as its low level container runtime. Today however, libcontainer is an Open Source project and it is a component of the runc container runtime. Another Open Source project started by Docker is containerd. As a Docker Engine component, containerd is responsible for unpacking the container image bundle and for invoking runc to run the container.

Docker implements the OCI specification, and it provides the tools necessary to bundle applications into digitally signed OCI container images, to interact with container image registries, and to manage the containers’ lifecycle from creation to removal, and all of their dependencies in between.

Docker is powered by the Docker Engine - a client-server application that runs on several Linux distributions and on Windows systems as well. The Docker Engine is composed of the Docker host running the Docker daemon, a REST API to communicate with the daemon, and a Docker client. Additionally, one or multiple registries are present too.


### rkt

[rkt](https://coreos.com/rkt/)  is an application container engine developed for modern production cloud-native environments. It features a pod-native approach, a pluggable execution environment, and a well-defined surface area that makes it ideal for integration with other systems.


* simpler application container runtime that implements the modern App Container (appc) specification and relies on the Application Container Image (ACI) format

* in recent years rkt adopted support for container images created in different formats, such as the Docker container images

* rkt is an application container engine developed for modern production cloud-native environments

* rkt is interoperable as well, being able to query multiple container image registries, such as quay of CoreOS, Docker Hub, while also allowing users to fetch container images by providing a direct URL of the desired container image.

Despite its initial popularity, the rkt project reached its end. The community’s interest in the rkt project declined, and the Cloud Native Computing Foundation (CNCF) decided to archive the project. Although no longer maintained, we will still explore various features of the rkt runtime.

### cri-o

[cri-o](https://cri-o.io/) is a lightweight container runtime for kubernetes

* cri-o is a minimal implementation of the Container Runtime Interface (CRI) to enable the usage of any Open Container Initiative (OCI) compatible runtime with Kubernetes

* As a lightweight alternative to using Docker or rkt as the runtimes for Kubernetes, it supports both GPG signed and unsigned container images

* The CRI-O runtime has been optimized for Kubernetes, and it also implements the Container Network Interface (CNI) for networking and supports CNI plugins

### other notable container runtimes

* [LXC](https://linuxcontainers.org/lxc/introduction/) and [LXD](https://linuxcontainers.org/lxd/introduction/)

* [kata containers](https://katacontainers.io/)

* [gVisor](https://gvisor.dev/)

* [podman](https://podman.io/)

## Quiz Questions

The App Container standard specifies the following (Select all answers that apply):

* App Container Image

* App Container Pod

* App Container Image Discovery

* App Container Executor


The Open Container Initiative includes specifications for (Select all answers that apply):

* Image format 
* Runtime

Running a container with runc requires a daemon process. True or False?
* false

Docker daemon manages runc containers through:

* containerd

Docker client communicates with Docker daemon using:

* REST APIs

A rkt pod can point to one or more applications. True or False?

* true

Docker Desktop on Mac and Windows allows the mounting of a host directory inside a container. True or False?

* true

Docker Desktop installs which of the following components (Select all answers that apply)?

* Docker Compose

* CLI Client

* Docker Engine

Docker Client is capable of communicating with a remote Docker Daemon. True or False?

* true