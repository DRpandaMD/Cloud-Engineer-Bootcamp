# Container Networking

* [Home](/README.md)


## Quite a few things to learn

* Discuss the Container Network Model and libnetwork. 
* Discuss the Container Network Interface and CNI plugins. 
* Describe the runc networking. 
* Discuss networking drivers supported by Docker. 
* Describe the network implementation for CRI-O.
* Understand the usage of various Docker and rkt networking commands.

## Standards and Specifications

The container network enables communication between microservices running inside containers, containers running on the same host, running on different hosts, or a container and other applications and services from anywhere in the world. Connected containers are also easier to manage and monitor

There are two container networking standards we need to be concerned about: the **Container Network Model (CNM)** and the **Container Network Interface (CNI)**. Both networking models present pros and cons in terms of adoption and level of support

For more reading about this see this post[THE CONTAINER NETWORKING LANDSCAPE: CNI FROM COREOS AND CNM FROM DOCKER](https://thenewstack.io/container-networking-landscape-cni-coreos-cnm-docker/)


## The Container Network Model

The Container Network Model (CNM) is the container network specification introduced by Docker and implemented by the **libnetwork** project. Companies and other projects such as Cisco, Calico, Weave, Kuryr, VMware, and Open Virtual Networking (OVN) adopted the CNM.

See the [libnetwork docs page](https://github.com/moby/libnetwork/blob/master/docs/design.md) and the [libnetwork project github page](https://github.com/moby/libnetwork)

By default, libnetwork supports drivers such as:

* **bridge** - implementing a Linux bridge
* **overlay** - implementing a network spanning multiple hosts using VXLAN network encapsulation
* **null** - for cases where no networking is required
* it also supports a **remote** package for custom network plugins. 

## The Container Network Interface

The Container Network Interface (CNI), a Cloud Native Computing Foundation Incubator project, is the container network specification introduced by CoreOS, adopted by projects such as Mesos, Amazon ECS, Kubernetes, Cloud Foundry, OpenShift, rkt, and supported by projects such as Calico, Cilium, Romana, CNI-Genie, VMware NSX, and Weave. It links container platforms such as Kubernetes and Cloud Foundry to multiple distinct container network implementations.

View the [CNI Spec Github Documentation](https://github.com/containernetworking/cni/blob/master/SPEC.md)


## CNI Plugins

Hop over to the [CNI plugins page](https://github.com/containernetworking/plugins#plugins)

### Plugin Groups

#### Main

* **bridge** interface
* **ipvlan**
* **loopback**
* **macvlan**, which creates a new MAC address and forwards all traffic targeting it to a container
* **ptp**, to create a `veth` pair
* **vlan**
* **host-device**

And for windows -- **SIGH**

* win-bridge
* win-overlay

#### IPAM

* dhcp plugin that makes DHCP requests for the container
* host-local to maintain a database of allocated IP addresses
* static plugin to allocate IPv4/IPv6 addresses to a container

#### Meta

* flannel compatibility
* sysctl tuning
* portmap from the host’s address space to the container
* bandwidth limitation with traffic control tbf
* sbr, for source-based routing configuration
* firewall for traffic rules in iptables or firewalld.


## Container Networking With Docker

One key takeaway was the driver/plugin approach to implementing the CNM via libnetwork.

Regardless of the network type and driver combination used to implement the container networking, Docker is able to manage traffic to and from containers by editing iptables and server routing rules, while providing advanced features such as packet encapsulation, encryption, routing mesh and session affinity.

## Docker Networking Drivers

### Bridge

The default network type that Docker containers attach to is the bridge network, implemented by the bridge driver. The main roles of a bridge network are to isolate the container network from the host system network, and to act as a DHCP server and assign unique IP addresses to containers as they are running attached to the bridge. This is a useful feature when containers of an application need to talk to each other while they all run on the same host system. Other features of bridge networks depend whether the bridge is default or user-defined. Most often, a user-defined bridge presents advantages over the default bridge, such as better traffic isolation, automatic DNS resolution, on-the-fly container connection/disconnection to and from the network, advanced and flexible configuration options, and even sharing of environment variables between containers.

### Host

The host network driver option, as opposed to the bridge, eliminates the network isolation between the container and the host system by allowing the container to directly access the host network. In addition, it may help with performance optimization by eliminating the need for Network Address Translation (NAT) or proxying since the container ports are automatically published and available as host ports

### Overlay 

Gaining a lot of popularity these days is the overlay network type supported by the overlay driver. It is the network type that spans multiple hosts, typically part of a cluster, allowing the containers' traffic to be routed between hosts as containers or services from one host attempt to talk to others running on another host in the cluster.

This network type is very popular with container orchestration platforms because it not only enables traffic across the entire cluster of host systems, but also provides traffic isolation and management through rules and policies.

### Macvlan

The macvlan network driver allows a user to change the appearance of a container on the physical network. A container may appear as a physical device with its own MAC address on the network, thus enabling the container to be directly connected to the physical network instead of having its traffic routed through the host network

### None

The none driver option for container networking disables the networking of a container while allowing the very same container to use a custom third-party network driver, if needed, to implement its networking requirements

### Network Plugins

Network plugins expand the capabilities of Docker through third-party network drivers that integrate Docker with specialized network stacks. Certain network plugins may combine features otherwise available from single network drivers while improving network resiliency and policy management.

## Container Networking with rkt

* **RKT is a dead project**

### host mode

In host mode, the microservice running in the rkt pod inherits the network namespace of the process that invoked rkt, which could be directly from the host system. When rkt is invoked by the host system, the microservice from the rkt pod shares the host system network stack. While in host mode, no isolation is available between the host and the pod networks, the pod having access to the host’s IP address, routes, and iptables

### Contained Mode

The contained network mode implies that a rkt pod runs in a separate network namespace with the network aided by the Container Network Interface (CNI) and CNI plugins.

#### Built-in networks

Rkt comes with two built-in networks. The first built-in network, the default network, consists of a loopback device and a veth device. The default network veth pair enables a point-to-point link between the rkt pod and the host system, while rkt sets the default route in the pod namespace and enables the host’s IP masquerading for Network Address Translation (NAT) of the outgoing traffic. There are several default built-in network types supporting the veth pair, such as ptp (point-to-point) for pod-to-host networking, bridge, macvlan (that assigns a random MAC address to a pod and does not provide node to pod communication) and ipvlan (providing IP address distinction).

The second built-in network, the default-restricted network, only allows the communication with the host system through the veth interface

#### No networking -- loopback only

In order to completely isolate a pod’s network, it can be placed in a namespace only with the loopback device.

#### Additional Networks

Rkt pods may join additional networks on top of the default network veth by configuring additional interfaces in the pod. When configuring additional networks, users are able to specify the network type based on available plugins, and IP address management (ipam) options such as IP address assignment, gateways and routes.

The ipam section of a network configuration file allows a user to specify the IP address allocation policy, gateway and route. By default, rkt offers two ipam types: host-local and DHCP. However, additional ipam types can be implemented through third-party plugins.

Unlike DHCP, the host-local ipam type allocates IP addresses from a range based on a static configuration, and it supports both IPv4 and IPv6 addresses.

For DHCP ipam type, the CNI DHCP plugin runs a client daemon on the host system to proxy between a container DHCP client and network DHCP service and to renew leases when required


#### Other plugins

Part of rkt’s flexibility is its capability of working with custom third-party plugins that implement the Container Network Interface (CNI). These plugins are just binaries parsing JSON configuration files which include network configuration specifications. There is an ever growing list of projects developing and maintaining CNI plugins, such as: Flannel, Calico, Weave, Cilium, Romana, and VMware-nsx

## Container Networking with CRI-O

CRI-O, the Kubernetes container runtime, has its networking designed to work with Kubernetes pods. Pod networking is set up through the Container Network Interface (CNI). The CNI enables containers to connect to a network when created and then remove resources upon containers’ deletion. It also makes it possible for any network plugin (also called CNI plugin) to work with CRI-O. CNI plugins such as Flannel, Weave, OpenShift-SDN have been tested and performed successfully with CRI-O.

When the CRI-O daemon is started by the crio command, there are options to specify the host network IP and to manage the network namespace

## Lab To be Revisited 