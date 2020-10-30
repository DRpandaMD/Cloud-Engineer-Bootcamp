# API Objects

* [Home](/README.md)

## What to learn

* Explore API versions.

* Discuss rapid change and development. -- much needed for modern IT organizations

* Deploy and configure and application using a Deployment. -- the basics of what makes k8s useful

* Examine primitives for a self-healing application. -- also much needed for modern IT organizations

* Scale an application. -- who doesn't like scaling 

## Overview 

This section is about additional API resources or objects. We will learn about resources in the `v1` API group, among others. Stability increases and code becomes more stable as objects move from `alpha` versions, to `beta`, and then `v1`, indicating stability. 

DaemonSets, which ensure a Pod on every node, and and StatefulSets, which stick a container to a node and otherwise act like a deployment, have progressed to `apps/v1` stability. Jobs and CronJobs are now in `batch/v1`.

Role-Based Access Control (RBAC), essential to security, has made the leap from `v1alpha1` to the stable `v1` status in one release. 

### Look at the release notes

If you been around any type of changing software, online video games for example.  You will know already the importance of checking the release notes and patch notes.  Like wise you should be reading the release notes for kubernetes. 

Any possible changes can be an important part of the ongoing system administration. Release notes, as well as discussions to release notes, can be found in version-dependent subdirectories in the [Features tracking repository for Kubernetes releases on GitHub](https://github.com/kubernetes/enhancements).

You can also check the [Kubernetes Release Notes](https://kubernetes.io/docs/setup/release/notes/#kubernetes-v1-17-0-release-notes)


## v1 API Group

The `v1` API group is no longer a single group, but rather a collection of groups for each main object category. For example, there is a `v1` group, a `storage.k8s.io/v1` group, and an `rbac.authorization.k8s.io/v1`, etc. Currently, there are eight v1 groups.

### Objects Examples

* Node
  * Represents a machine - physical or virtual - that is part of your Kubernetes cluster. You can get more information about nodes with the `kubectl` get nodes command. You can turn on and off the scheduling to a node with the `kubectl cordon/uncordon` commands.

* Service Account
  * Provides an identifier for processes running in a pod to access the API server and performs actions that it is authorized to do.

* Resource Quota
  * It is an extremely useful tool, allowing you to define quotas per namespace. For example, if you want to limit a specific namespace to only run a given number of pods, you can write a `resourcequota` manifest, create it with `kubectl` and the quota will be enforced.

* Endpoint
  * Generally, you do not manage endpoints. They represent the set of IPs for pods that match a particular service. They are handy when you want to check that a service actually matches some running pods. If an endpoint is empty, then it means that there are no matching pods and something is most likely wrong with your service definition.



## Deploying an Application

Using the `kubectl create` command, we can quickly deploy an application. We have looked at the Pods created running the application, like nginx. Looking closer, you will find that a Deployment was created, which manages a ReplicaSet, which then deploys the Pod.

## Objects used with app deployment

* Deployment
  * It is a controller which manages the state of ReplicaSets and the pods within. The higher level control allows for more flexibility with upgrades and administration. Unless you have a good reason, use a deployment.

* ReplicaSet
  * Orchestrates individual pod lifecycle and updates. These are newer versions of Replication Controllers, which differ only in selector support.

* Pod
  * it is the lowest unit you can manage; it runs the application container, and possibly support containers.

## DaemonSets

Should you want to have a logging application on every node, a DaemonSet may be a good choice. The controller ensures that a single pod, of the same type, runs on every node in the cluster. When a new node is added to the cluster, a Pod, same as deployed on the other nodes, is started. When the node is removed, the DaemonSet makes sure the local Pod is deleted. DaemonSets are often used for logging, metrics and security pods, and can be configured to avoid nodes.  For more reading checkout [K8s DaemonSet Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)


A DaemonSet ensures that all (or some) Nodes run a copy of a Pod. As nodes are added to the cluster, Pods are added to them. As nodes are removed from the cluster, those Pods are garbage collected. Deleting a DaemonSet will clean up the Pods it created.

Some typical uses of a DaemonSet are:

* running a cluster storage daemon on every node
* running a logs collection daemon on every node
* running a node monitoring daemon on every node

In a simple case, one DaemonSet, covering all nodes, would be used for each type of daemon. A more complex setup might use multiple DaemonSets for a single type of daemon, but with different flags and/or different memory and cpu requests for different hardware types.


## Satefulsets

Check out the [StatefulSets Docs](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)

StatefulSet is the workload API object used to manage stateful applications.

Manages the deployment and scaling of a set of Pods, and provides guarantees about the ordering and uniqueness of these Pods.

Like a Deployment, a StatefulSet manages Pods that are based on an identical container spec. Unlike a Deployment, a StatefulSet maintains a sticky identity for each of their Pods. These pods are created from the same spec, but are not interchangeable: each has a persistent identifier that it maintains across any rescheduling.

If you want to use storage volumes to provide persistence for your workload, you can use a StatefulSet as part of the solution. Although individual Pods in a StatefulSet are susceptible to failure, the persistent Pod identifiers make it easier to match existing volumes to the new Pods that replace any that have failed.

StatefulSets are stable as of Kubernetes v1.9.​


## Autoscaling

In the autoscaling group we find the **Horizontal Pod Autoscalers (HPA)**. This is a stable resource. HPAs automatically scale Replication Controllers, ReplicaSets, or Deployments based on a target of 50% CPU usage by default. The usage is checked by the kubelet every 30 seconds, and retrieved by the Metrics Server API call every minute. HPA checks with the Metrics Server every 30 seconds. Should a Pod be added or removed, HPA waits 180 seconds before further action. 

Other metrics can be used and queried via REST. The autoscaler does not collect the metrics, it only makes a request for the aggregated information and increases or decreases the number of replicas to match the configuration.

The **Cluster Autoscaler** (CA) adds or removes nodes to the cluster, based on the inability to deploy a Pod or having nodes with low utilization for at least 10 minutes. This allows dynamic requests of resources from the cloud provider and minimizes expenses for unused nodes. If you are using CA, nodes should be added and removed through `cluster-autoscaler-` commands. Scale-up and down of nodes is checked every 10 seconds, but decisions are made on a node every 10 minutes. Should a scale-down fail, the group will be rechecked in 3 minutes, with the failing node being eligible in five minutes. The total time to allocate a new node is largely dependent on the cloud provider. 

There is also Vertical Pod Autoscaling where it will adjust the CPU and MEM usage up or down on the pods.  Check out the [Vertical Pod Auto Scaling Google Doc Page](https://cloud.google.com/kubernetes-engine/docs/concepts/verticalpodautoscaler)

## Jobs

Straight from the K8s documentation 

A Job creates one or more Pods and ensures that a specified number of them successfully terminate. As pods successfully complete, the Job tracks the successful completions. When a specified number of successful completions is reached, the task (ie, Job) is complete. Deleting a Job will clean up the Pods it created.

A simple case is to create one Job object in order to reliably run one Pod to completion. The Job object will start a new Pod if the first Pod fails or is deleted (for example due to a node hardware failure or a node reboot).

You can also use a Job to run multiple Pods in parallel.  See more plus examples at the [Kubernetes Docs Job Page](https://kubernetes.io/docs/concepts/workloads/controllers/job/)

Jobs are part of the `batch` API group. They are used to run a set number of pods to completion. If a pod fails, it will be restarted until the number of completion is reached.

While they can be seen as a way to do batch processing in Kubernetes, they can also be used to run one-off pods. A Job specification will have a parallelism and a completion key. If omitted, they will be set to one. If they are present, the parallelism number will set the number of pods that can run concurrently, and the completion number will set how many pods need to run successfully for the Job itself to be considered done. Several Job patterns can be implemented, like a traditional work queue.

Cronjobs work in a similar manner to Linux jobs, with the same time syntax. There are some cases where a job would not be run during a time period or could run twice; as a result, the requested Pod should be idempotent.

An option spec field is `.spec.concurrencyPolicy`, which determines how to handle existing jobs, should the time segment expire. If set to `Allow`, the default, another concurrent job will be run. If set to Forbid, the current job continues and the new job is skipped. A value of Replace cancels the current job and starts a new job in its place.

## Role Based Access Control (RBAC)

From the K8s docs:

Role-based access control (RBAC) is a method of regulating access to computer or network resources based on the roles of individual users within your organization.

RBAC authorization uses the `rbac.authorization.k8s.io` API group to drive authorization decisions, allowing you to dynamically configure policies through the Kubernetes API.

To enable RBAC, start the API server with the `--authorization-mode` flag set to a comma-separated list that includes `RBAC`; for example:

`kube-apiserver --authorization-mode=Example,RBAC --other-options --more-options`

#
