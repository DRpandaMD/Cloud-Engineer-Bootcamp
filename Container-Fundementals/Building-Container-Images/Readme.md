# Building Container Images

* [Home](/README.md)

## Things to Learn

* Describe methods to build container images.

* Describe the image build process with Dockerfile and Containerfile.

* Discuss the CMD and ENTRYPOINT instructions of Dockerfile.

* Discuss Docker, Buildah, and Podman.

## Build it from scratch

Creating a container image from scratch implies that all components of the image are specified manually or through scripts. The approaches vary when it comes to building images from scratch. Some tools require certain steps to be performed by the user prior to specific instruction can be issued by the runtime while other tools support all aspects of the container image build. There are several tools that manage various aspects of the image build process from scratch, such as runc, Docker, Buildah, Podman, Distrobuilder, Goaci, and Acbuild.

## Dockerfile

### Dockerfile Instruction Formats

* Shell Form
  * The commands specified as arguments of the CMD instruction run inside a shell. Here is an example of the CMD instruction with arguments in shell form:

  * CMD echo "We are learning about Dockerfile"

  * On Linux, the instruction would look like this:

  * /bin/sh -c echo "We are learning about Dockerfile"

  * The default shell on Linux is /bin/sh/ -c. On Windows it is cmd /S /C​. However, we can change the default shell with the SHELL​ instruction of the Dockerfile.

* Exec Form
  * In this form, the binary/program is run directly, without a shell. Here is an example of the CMD instruction with arguments in exec form:

  * CMD ["/bin/echo", "We are learning about Dockerfile"]

  * The binary /bin/echo​ runs directly inside the container, and participates in the build process. Between the two forms, the Exec Form is preferred by users.

### Dockerfile Instructions

There are a LOT!  Check the docks [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)

Also for reference check the dockerfiles on [DockerHub](https://hub.docker.com/)

In the build process use a `.dockerignore` file to ignore directories 



### Quiz

Again with the quiz it seemed a bit off with the questions they were asking

A container image is a runtime object. True or False?  **FALSE**

Which of the following are image creation methods? Select all answers that apply.

* build from scratch

* from a running container

* image conversion 

* Containers can be created from images, and images can be created from containers

A container image can be created from (Select all answers that apply)

* a running container

* base container image

* File containg set of instructions

Instead of sharing container images, what other objects can we share to enable image builds? Select all answers that apply

* Configfiles -- lol what?

* Dockerfiles

Dockerfiles and Containerfiles include build time instructions 

* From
* Run
* Shell

Dockerfiles and Containerfiles include run time instructions 

* Cmd
* Entrypoint 