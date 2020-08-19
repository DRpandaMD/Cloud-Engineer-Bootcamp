# Kubernetes Fundamentals

* [Home](/README.md)

## What to learn

* Understand basics of what Kubernetes is

* Learn Basic Kubernetes terminology 

* discuss configuration tools

* Learn about the community resources available 

## What is kubernetes

Checkout [Kubernetes.io](https://kubernetes.io/)

Basically, K8s is *"an open-source system for automating deployment, scaling, and management of containerized applications"*


A key aspect of Kubernetes is that it builds on 15 years of experience at Google in a project called borg. 

Google's infrastructure started reaching high scale before virtual machines became pervasive in the datacenter, and containers provided a fine-grained solution for packing clusters efficiently. Efficiency in using clusters and managing distributed applications has been at the core of Google challenges

In Greek, κυβερνητης means the Helmsman, or pilot of the ship. Keeping with the maritime theme of Docker containers, Kubernetes is the pilot of a ship of containers. Due to the difficulty in pronouncing the name, many will use a nickname, K8s, as Kubernetes has eight letters between K and S


## Components of Kubernetes 

Instead of using a large server, Kubernetes approaches the same issue by deploying a large number of small web servers, or microservices. The server and client sides of the application are written to expect that there are many possible agents available to respond to a request. It is also important that clients expect the server processes to die and be replaced, leading to a transient server deployment. Instead of a large Apache web server with many httpd daemons responding to page requests, there would be many nginx servers, each responding

The transient nature of smaller services also allows for decoupling. Each aspect of the traditional application is replaced with a dedicated, but transient, microservice or agent. To join these agents, or their replacements together, we use services and API calls. A service ties traffic from one agent to another (for example, a frontend web server to a backend database) and handles new IP or other information, should either one die and be replaced. 

Communication is entirely API call-driven, which allows for flexibility. Cluster configuration information is stored in a JSON format inside of etcd, but is most often written in YAML by the community. Kubernetes agents convert the YAML to JSON prior to persistence to the database

* Fun Fact : Kubernetes is written in Go see [golang.org](https://golang.org/)


## Other Solutions to Kubernetes

* Docker Swarm

* Apache Mesos

* Nomad

* Rancher

* Not Listed (Openshift)

Openshift and Rancher are BOTH layers on top of Kubernetes, which they don't mention.  There *are* operational tasks that need to take place that Vanilla Kubernetes does not offer out of the box.

## Kubernetes Architecture

See the [Kubernetes Docs](https://kubernetes.io/docs/concepts/architecture/cloud-controller/)

In its simplest form, Kubernetes is made of a central manager (aka master) and some worker nodes, once called minions (we will see in a follow-on chapter how you can actually run everything on a single node for testing purposes). The manager runs an API server, a scheduler, various controllers and a storage system to keep the state of the cluster, container settings, and the networking configuration

The cloud-controller-manager is a Kubernetes control plane component that embeds cloud-specific control logic. The cloud controller manager lets you link your cluster into your cloud provider's API, and separates out the components that interact with that cloud platform from components that just interact with your cluster.

By decoupling the interoperability logic between Kubernetes and the underlying cloud infrastructure, the cloud-controller-manager component enables cloud providers to release features at a different pace compared to the main Kubernetes project.

Kubernetes exposes an API via the API server. You can communicate with the API using a local client called kubectl or you can write your own client and use curl commands. The kube-scheduler is forwarded the requests for running containers coming to the API and finds a suitable node to run that container. Each node in the cluster runs two processes: a kubelet and kube-proxy. The kubelet receives requests to run the containers, manages any necessary resources and watches over them on the local node. The kubelet interacts with the local container engine, which is Docker by default, but could be rkt or cri-o, which is growing in popularity

## Resources Recommendations

* [Borg Whitepaper](https://research.google/pubs/pub43438/)

* [Borg and Kubernetes Podcast with John Wilkes](https://www.gcppodcast.com/post/episode-46-borg-and-k8s-with-john-wilkes/)

* [Github Kubernetes Community Page](https://github.com/kubernetes/community)

* [Kubernetes Slack](http://slack.kubernetes.io/)

* [Stack Overflow](https://stackoverflow.com/search?q=kubernetes)