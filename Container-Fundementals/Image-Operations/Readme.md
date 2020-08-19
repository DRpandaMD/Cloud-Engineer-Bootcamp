# Image Operations

* [Home](/README.md)

## What to learn 

* understand container images.
* Understand image registries and repositories
* Understand image operations in general
* Explain different operations supported by container runtimes and tools such as runc, Docker, rkt, Buildah and Podman

## Container Images 

A container image is a template for a running container and it is created in the form of a tarball with configuration files. The image includes container configuration options and runtime settings to guide the container’s behavior at runtime. Container runtimes load images to run them as containers, therefore at runtime, a container becomes a running instance of an image, and there may be many containers started from the same image.

As of right now in 2020, if you google "container images" it leads you straight to [Docker (software) Wikipedia](https://en.wikipedia.org/wiki/Docker_(software)). From a general point of view the concepts are more or less the same.  Just the implementation from major container image to another might be slightly different.  As usual, hit up the technology docs to dig deeper.  If you don't find what you are looking for, then it might mean that said technology needs to mature a bit more and  you should spend time using something a little more mainstream. 

## Image Registries and Repositories 

* Container images once created, are stored in a location where they can be accessed for further development and updates, and to be shared by multiple users across different hosts, as developers and testers alike need to have access to images during various development and testing phases

* This shared storage location, paired with a software solution that supports security features and version control via tags for the images it stores, is called an image registry.

* An image registry is an efficient and secure storage option for images that could be setup in the cloud or on-prem

* Public registries prevent users from re-inventing the wheel, offering the benefit of reusability of images that are generally available to use and modify

* Private (on-prem) registries are commonly used by enterprises as they provide more privacy and security, as an ideal solution that addresses application code security concerns.

* A registry offers image protection features through Role Based Access Control (RBAC) policies, digital signatures for authenticity, and possibly vulnerability scans.

### Common Registries

* [Docker Hub](https://hub.docker.com/)

* [Quay Container Registry](https://quay.io/)

* [Amazon ECR](https://aws.amazon.com/ecr/)

* [Google Container Registry](https://cloud.google.com/container-registry)

* [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/)

My top picks are Docker Hub and Google Container Registry.


## Container Image Operations

### runc

[runc docs](https://github.com/opencontainers/runc)

The runc container runtime runs containers bundled in the OCI format, based on a JSON configuration file. As a low-level runtime, runc is capable of reading a predefined default JSON file or allows us to manually generate the configuration ourselves. Runc offers a limited set of operations that allow users to build the OCI formatted bundle which includes all necessary files for the creation and running of a container.

To create the default configuration file we can start from an existing Docker container image, export its root filesystem and populate it into a newly generated JSON configuration file, readable by runc. Subsequently, runc generates the JSON configuration file, also known as the spec

### Docker

[Docker CLI](https://docs.docker.com/engine/reference/commandline/docker/)

**Docker** is my *current* favorite, and I think its what most people have experience with.

The key component required for the creation of a container image is the **Dockerfile**. It includes a set of instructions that are executed by the **Docker daemon** to build the image. In addition to Dockerfile, a **context** representing a set of files stored in a local directory or a remote repository is also needed for the build. The key Dockerfile instructions are:

* **FROM** to initialize the image build and specify the base image used as starting point for the current build,

* **COPY** to populate the container’s filesystem with existing directories or files from the host system,

* **RUN** to execute commands that alter the base image and commit the changes

* **CMD** to provide defaults for the running container

* **ENTRYPOINT** to configure a container’s command line arguments by overriding defaults specified by CMD

* **LABEL** to add metadata in a key-value pair format to the image,

* **EXPOSE** to specify the port number and protocol which should be published as options with the docker run command

* **ENV** to set environment variables in a key-value pair format

* **VOLUME** to mount storage in the container

* **USER** to set the user name and possibly the group name at runtime

we may display the history of an image build with `docker image history`, allowing us to see the previous image layers and some of their details

`docker images` command, however, displays only a listing of the top level images with their cumulative disk sizes - that is the sizes of all the layers that make up the final, top level, image

### rkt

[rkt documentation](https://github.com/rkt/rkt) is now archived ]

see [github rkt archival](https://github.com/rkt/rkt/issues/4024)

his ticket is both a project status update and a tracker for the next steps.

Quoting my last status report from last year:

For reference, the previous development team at CoreOS got dismantled, and post Red Hat acquisition there are no plan to push the development forward.

However @kinvolk still has some development plans for it. Plus we acknowledge the fact that there are plenty of stable deployments out there where rkt fits well.

For clarity the github.com/rkt/rkt project will keep existing as a standalone free-software project, but it won't be anymore under CNCF umbrella.

Since then, a few things happened in the ecosystem around rkt:

an End-Of-Life notice has been put out for CoreOS Container Linux
@kinvolk privately told me that they no more have development plans for rkt, and @blixtra plans to deprecate its usage in Flatcar
no major development or community engagement showed up in several months
For these reasons, there is now a rough consensus to proceed to declare the end of this project.

### Quiz and Labs - Revisit

* the Quiz asked really dumb questions. Labs is just executing commands.