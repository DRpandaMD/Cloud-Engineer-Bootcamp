# Installation and Configuration

* [Home](/README.md)

## What to learn

* Download installation and configuration tools.

* Install a Kubernetes master and grow a cluster.

* Configure a network solution for secure communications.

* Discuss highly-available deployment considerations.

## Installation Tools

* One way is to use Google Container Engine (GKE), a cloud service from the Google Cloud Platform, that lets you request a Kubernetes cluster with the latest stable version.

* Another easy way to get started is to use Minikube. It is a single binary which deploys into the Oracle VirtualBox software, which can run in several operating systems. While Minikube is local and single node, it will give you a learning, testing, and development platform. MicroK8s is a  newer tool tool developed by Canonical, and aimed at easy installation. Aimed at appliance-like installations, it currently runs on Ubuntu 16.04 and 18.04. 


* To be able to use the Kubernetes cluster, you will need to have installed the Kubernetes command line, called **kubectl**. This runs locally on your machine and targets the API server endpoint. It allows you to create, manage, and delete all Kubernetes resources (e.g. Pods, Deployments, Services). It is a powerful CLI that we will use throughout the rest of this course. So, you should become familiar with it.

* kubeadm, the community-suggested tool from the Kubernetes project, that makes installing Kubernetes easy and avoids vendor-specific installers. Getting a cluster running involves two commands: kubeadm init, that you run on one Master node, and then, kubeadm join, that you run on your worker or redundant master nodes, and your cluster bootstraps itself. The flexibility of these tools allows Kubernetes to be deployed in a number of places. Lab exercises use this method. 

## Installing kubectl

* [See Kubectl Install Docs](https://kubernetes.io/docs/tasks/tools/install-kubectl/)  They have a wide array of how to install it. 

* You can (if needed) download the code from GitHub, and go through the usual steps to compile and install kubectl.

* This command line will use ` ~/.kube/config` as a configuration file. This contains all the Kubernetes endpoints that you might use. If you examine it, you will see cluster definitions (i.e. IP endpoints), credentials, and contexts.

* A context is a combination of a cluster and user credentials. You can pass these parameters on the command line, or switch the shell between contexts with a command

`kubectl config use-context myContext`


## Using GKE

You will need:

* A Google Cloud Account

* Payment Method

* The gcloud cli installed OR just used the google cloud shell (its free)

You can follow the [GKE Quickstart](https://cloud.google.com/kubernetes-engine/docs/quickstart)

```bash

gcloud container clusters create cluster-name --num-nodes=1


gcloud container clusters get-credentials cluster-name

```

## Using Minikube

Minikube is an open source project within the GitHub Kubernetes organization. While you can download a release from GitHub, following listed directions, it may be easier to download a pre-compiled binary. Make sure to verify and get the latest version see [Github Minikube](https://github.com/kubernetes/minikube#minikube)


## Using Kubeadm

See the [kubeadm docs](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/) on how to "Create a single control-plane cluster with kubeadm"

## Installing a Pod Network

Prior to initializing the Kubernetes cluster, the network must be considered and IP conflicts avoided. There are several Pod networking choices, in varying levels of development and feature set.

Many of the projects will mention the Container Network Interface (CNI), which is a CNCF project. Several container runtimes currently use CNI. As a standard to handle deployment management and cleanup of network resources, CNI will become more popular.


### Calico

A flat Layer 3 network which communicates without IP encapsulation, used in production with software such as Kubernetes, OpenShift, Docker, Mesos and OpenStack. Viewed as a simple and flexible networking model, it scales well for large environments. Another network option, Canal, also part of this project, allows for integration with Flannel. Allows for implementation of network policies. See the [Calico Project Page](https://www.projectcalico.org/) for more information.


### Flannel

A Layer 3 IPv4 network between the nodes of a cluster. Developed by CoreOS, it has a long history with Kubernetes. Focused on traffic between hosts, not how containers configure local networking, it can use one of several backend mechanisms, such as VXLAN. A flanneld agent on each node allocates subnet leases for the host. While it can be configured after deployment, it is much easier prior to any Pods being added. See the [flannel github](https://github.com/coreos/flannel#flannel) for more information. 


### Kube-Router

Feature-filled single binary which claims to "do it all". The project is in the alpha stage, but promises to offer a distributed load balancer, firewall, and router purposely built for Kubernetes. See the [Kube-Router Page](https://www.kube-router.io/)


### Romana

This is another project aimed at network and security automation for cloud native applications. Aimed at large clusters, IPAM-aware topology and integration with kops clusters.  See the [Romana Github page](https://github.com/romana/romana#romana---network-and-security-automation-solution-for-cloud-native-applications)

### Weave Net

It is typically used as an add-on for a CNI-enabled Kubernetes cluster. See [Weave Net](https://www.weave.works/oss/net/)


## More Installation tools

### Kubespray 

kubespray is now in the Kubernetes incubator. It is an advanced Ansible playbook which allows you to set up a Kubernetes cluster on various operating systems and use different network providers. ​It was once known as kargo. See [kubespray Github](https://github.com/kubernetes-sigs/kubespray#deploy-a-production-ready-kubernetes-cluster)

### kops

kops (Kubernetes Operations) lets you create a Kubernetes cluster on AWS via a single command line. Also in beta for GKE and alpha for VMware. See [kops github](https://github.com/kubernetes/kops#kops---kubernetes-operations)


### kube-aws

kube-aws is a command line tool that makes use of the AWS Cloud Formation to provision a Kubernetes cluster on AWS.  [kube-aws](https://kubernetes-incubator.github.io/kube-aws/)


### kubicorn

kubicorn is a tool which leverages the use of kubeadm to build a cluster. It claims to have no dependency on DNS, runs on several operating systems, and uses snapshots to capture a cluster and move it. [kubeicorn](http://kubicorn.io/)


## Main Deployment Configurations

### Single-node

With a single-node deployment, all the components run on the same server. This is great for testing, learning, and developing around Kubernetes.

### Single head node, multiple workers

Adding more workers, a single head node and multiple workers typically will consist of a single node etcd instance running on the head node with the API, the scheduler, and the controller-manager.

### Multiple head nodes with HA, multiple workers

Multiple head nodes in an HA configuration and multiple workers add more durability to the cluster. The API server will be fronted by a load balancer, the scheduler and the controller-manager will elect a leader (which is configured via flags). The etcd setup can still be single node.

### HA etcd, HA head nodes, multiple workers 

The most advanced and resilient setup would be an HA etcd cluster, with HA head nodes and multiple workers. Also, etcd would run as a true cluster, which would provide HA and would run on nodes separate from the Kubernetes head nodes.


## Compling from source -- LOL

The list of binary releases is available on GitHub. Together with gcloud, minikube, and kubeadmin, these cover several scenarios to get started with Kubernetes.

Kubernetes can also be compiled from source relatively quickly. You can clone the repository from GitHub, and then use the Makefile to build the binaries. You can build them natively on your platform if you have a Golang environment properly set up, or via Docker containers if you are on a Docker host.

To build natively with Golang, first install Golang. Download files and directions can be found online.

Once Golang is working, you can clone the kubernetes repository, around 500MB in size. Change into the directory and use make:

```bash
cd $GOPATH

git clone https://github.com/kubernetes/kubernetes

cd kubernetes

make

```

On a Docker host, clone the repository anywhere you want and use the make quick-release command. The build will be done in Docker containers. 

The _output/bin directory will contain the newly built binaries.

## Installing Kubernetes from scratch labs

### Install Kubernetes

* start with a fresh Ubuntu 18 lts image on gcp (I used a `n1-standard-1 (1 vCPU, 3.75 GB memory)` instance) and ssh into it

* I used this `gcloud beta compute ssh --zone "us-central1-a" "kubemaster" --project "kubernetes-discovery"` to ssh into from the gcloud shell

* update and upgrade system, install a text editor and install docker

```bash
    1  apt update
    2  apt upgrade
    3  apt install vim
    4  which vim
    5  apt install docker.io
    6  history
```

* manually add repo entry for kubernetes

`vim /etc/apt/sources.list.d/kubernetes.list`  

* add the repo `deb  http://apt.kubernetes.io/  kubernetes-xenial  main`

* add GPG key for packages `curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -`

* with the new repo added you gotta update the system: `apt update`

* now install the software `apt install -y kubeadm=1.18.1-00 kubelet=1.18.1-00 kubectl=1.18.1-00`

* hold the software at the most recent version `apt-mark hold kubelet kubeadm kubectl` *the latest versions are not nearly as stable as the older versions.... so i am told*

* now we need to install a pod network here we are going to install Calico `wget https://docs.projectcalico.org/manifests/calico.yaml`

* next we need to add the ip address of the node to etc hosts

`ip addr show` and look for something like `inet 10.128.0.44/32 scope global dynamic ens4`

* copy that ip and put it in `/etc/hosts`

* now we have to create a *kubeadm-config.yaml*

```yaml
apiVersion: kubeadm.k8s.io/v1beta2
kind: ClusterConfiguration
kubernetesVersion: 1.18.1
controlPlaneEndpoint: "k8smaster:6443"
networking:
  podSubnet: 192.168.0.0/16
```

* now to try to run `kubeadm init`

`kubeadm init --config=kubeadm-config.yaml --upload-certs | tee kubeadm-init.out`

AND the lab broke here, I can not continue 

This is why I opt to used GKE instead of doing a manual install.

**GROSS**

* After some trouble shooting :

```bash
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

```

So lets do that!

* After you do those commands you should check out your work by `less .kube/conf`

Now Apply the network plugin configuration to your cluster. 
Remember to copy the file to the current, non-root user directory first.

* `sudo cp /root/calico.yaml`  and then `1kubectl apply -f calico.yaml`

* While many objects have short names, a `kubectl` command can be a lot to type. We will enable `bash auto-completion`. Begin by adding the settings to the current shell.  Then update the `/.bashrcfile` to make it persistent.  Ensure the `bash-completion` package is installed. If it was not installed, log out then back in for the shell completion to work.

```bash
sudo apt-get install bash-completion -y
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ./bashrc
```

* then you will need to test for bash completion `kubectl des <tab> nod <tab>`

* Congrats your K8s master server is up and running

### Grow the Cluster

* head back over to GCP and make duplicate of that instance and give it a new name

* update the system and install docker

* repeat the same install from before 

```bash
root@worker-01:~# history
    1  clear
    2  apt update
    3  apt upgrade
    4  apt install docker.io
    5  apt install vim
    6  vim /etc/apt/sources.list.d/kubernetes.list
    7  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    8  apt update
    9  apt install -y kubeadm=1.18.1-00 kubelet=1.18.1-00 kubectl=1.18.1-00
   10  apt-mark hold kubeadm kublet kubectl
   11  apt-mark hold kubeadm kubelet kubectl
   12  history
```

* Hop back  over to the master 

* make a token `sudo kubeadm token create`

* now you need to use a *Discovery Token CA Cert Hash*

```bash
openssl x509 -pubkey \
-in /etc/kubernetes/pki/ca.crt | openssl rsa \
-pubin -outform der 2>/dev/null | openssl dgst \
-sha256 -hex | sed 's/ˆ.* //'
```

* keep this output you will need it later

* swap back over to the worker node and insert into /etc/hosts the DNS hostname and IP 

* now you will need to use token and has from the previous steps on the worker node with kubeadm to join the node to the master



## Quiz Questions

What is the **kubeadm** command used for? -- Create a cluster and add nodes

Which of the following is the main binary for working with objects of a Kubernetes cluster? -- kubectl

How many pod networks can you have per cluster? Only 1

The ~/.kube/config file contains _____________.??? { Engpoints, SSL Keys, Contexts}