# Installation and Configuration

* [Home](/README.md)

## What to learn

* Understand the API REST-based architecture.

* Work with annotations.

* Understand a simple Pod template.

* Use kubectl with greater verbosity for troubleshooting.

* Separate cluster resources using namespaces.


## API Access

Kubernetes has a powerful REST-based API. *The entire architecture is API-driven*. Knowing where to find resource endpoints and understanding how the API changes between versions can be important to ongoing administrative tasks, as there is much ongoing change and growth. *Starting with v1.16 deprecated objects are no longer honored by the API server*.

The main agent for communication between cluster agents and from outside the cluster is the **kube-apiserver**. A `curl` query to the agent will expose the current API groups. Groups may have multiple versions, which evolve independently of other groups, and follow a domain-name format with several names reserved, such as single-word domains, the empty group, and any name ending in `.k8s.io`.

## A RESTful approach

kubectl makes API calls on your behalf, responding to typical HTTP verbs (GET, POST, DELETE). You can also make calls externally, using curl or other program. With the appropriate certificates and keys, you can make requests, or pass JSON files to make configuration changes.   For example

```bash
curl --cert userbob.pem --key userBob-key.pem \  
--cacert /path/to/ca.pem \   
https://k8sServer:6443/api/v1/pods 
```

The ability to impersonate other users or groups, subject to RBAC configuration, allows a manual override authentication. This can be helpful for debugging authorization policies of other users

## Checking Access

you may find it helpful to check the current authorizations, both as an administrator, as well as another user. The following shows what user `bob` could do in the `default` namespace and the `developer` namespace, using the auth `can-i` subcommand to query: 

```bash
kubectl auth can-i create deployments
yes 

kubectl auth can-i create deployments --as bob
no 

kubectl auth can-i create deployments --as bob --namespace developer
yes
``` 

There are currently three APIs which can be applied to set who and what can be queried:

* **SelfSubjectAccessReview​** -
  Access review for any user, helpful for delegating to others. 

* **LocalSubjectAccessReview** -
  ​Review is restricted to a specific namespace.

* **SelfSubjectRulesReview​** -
  A review which shows allowed actions for a user within a particular namespace. 


## Optimistic Concurrency 

The default serialization for API calls must be JSON. There is an effort to use Google's `protobuf` serialization, but this remains experimental. While we may write and edit files in a YAML format, they are *converted* to and from JSON. 

Kubernetes uses the `resourceVersion` value to determine API updates and implement optimistic concurrency. In other words, an object is not locked from the time it has been read until the object is written.

Instead, upon an updated call to an object, the `resourceVersion` is checked, and a `409 CONFLICT` is returned, should the number have changed. The `resourceVersion` is currently backed via the `modifiedIndex` parameter in the **etcd** database, and is unique to the namespace, kind, and server. Operations which do not change an object, such as `WATCH` or `GET`, do not update this value.

## Using Annotations

Labels are used to work with objects or collections of objects; annotations are not.

Instead, annotations allow for metadata to be included with an object that may be helpful outside of the Kubernetes object interaction. Similar to labels, they are key to value maps. They are also able to hold more information, and more human-readable information than labels. 

Having this kind of metadata can be used to track information such as a timestamp, pointers to related objects from other ecosystems, or even an email from the developer responsible for that object's creation. 

The annotation data could otherwise be held in an exterior database, but that would limit the flexibility of the data. The more this metadata is included, the easier it is to integrate management and deployment tools or shared client libraries. 

For example, to annotate only Pods within a namespace, you can overwrite the annotation, and finally delete it: 

```bash
kubectl annotate pods --all description='Production Pods' -n prod 

kubectl annotate --overwrite pods description="Old Production Pods" -n prod 

kubectl annotate pods foo description- -n prod
```

## A simple Pod

a Pod is the lowest compute unit and individual object we can work with in Kubernetes. It can be a single container, but often, it will consist of a primary application container and one or more supporting containers. 

Have a look at this baby yaml file

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: firstpod
spec:
    containers:
    - image: nginx
      name: stan 
```

* `apiVersion` - must match the existing api group

* `kind` - tells k8s what type of object to create

* `metadata` - at least have a name

* `spec` - what to create and the paramters

## Manage API Resources with kubectl

Kubernetes exposes resources via RESTful API calls, which allows all resources to be managed via HTTP, JSON or even XML, the typical protocol being HTTP. The state of the resources can be changed using standard HTTP verbs (e.g. GET, POST, PATCH, DELETE, etc.).

`kubectl` has a verbose mode argument which shows details from where the command gets and updates information. Other output includes curl commands you could use to obtain the same result. While the verbosity accepts levels from zero to any number, there is currently no verbosity value greater than ten. You can check this out for `kubectl get`. 

Some examples:

* `kubectl --v=10 get pods firstpod`

* `kubectl --v=10 delete pods firstpod`

## Access from Outside the Cluster

The primary tool used from the command line will be `kubectl`, which calls `curl` on your behalf. You can also use the `curl` command from outside the cluster to view or make changes. 

You can view basic server information with : `kubectl config view`

*Take note!!* Without the certificate authority, key and certificate from this file, only insecure curl commands can be used, which will not expose much due to security settings

## ~/.kube/config

Here is some of a sample `~/.kube/config` file:

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdF.....
    server: xxx.xxx.xxx.xxx:abcd
    name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: LS0tLS1CRUdJTib.....
    client-key-data: LS0tLS1CRUdJTi....
```

Lets go over some of the keys in this document.

* **apiVersion**
  * As with other objects, this instructs the `kube-apiserver` where to assign the data.

* **clusters**
  * This key contains the name of the cluster, as well as where to send the API calls. The `certificate-authority-data` is passed to authenticate the `curl` request.

* **contexts**
  * This is a setting which allows easy access to multiple clusters, possibly as various users, from one configuration file. It can be used to set `namespace`, `user`, and `cluster`.

* **current-context**
  * This shows which cluster and user the `kubectl` command would use. These settings can also be passed on a per-command basis.

* **kind**
  * Every object within Kubernetes must have this setting; in this case, a declaration of object type `Config`.

* **preferences**
  * Currently not used, this is an optional settings for the `kubectl` command, such as colorizing output.

* **users**
  * A nickname associated with client credentials, which can be client key and certificate, username and password, and a token. Token and username/password are mutually exclusive. These can be configured via the `kubectl config set-credentials` command.

## Namespaces

The term *namespace* is used to reference both the kernel feature and the segregation of API objects by Kubernetes. Both are means to keep resources distinct. 

Every API call includes a namespace, using `default` if not otherwise declared: `https://10.128.0.3:6443/api/v1/namespaces/default/pods`.

From the [Kubernetes Namespaces Docs Page](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)

**When to Use Multiple Namespaces**
Namespaces are intended for use in environments with many users spread across multiple teams, or projects. For clusters with a few to tens of users, you should not need to create or think about namespaces at all. Start using namespaces when you need the features they provide.

Namespaces provide a scope for names. Names of resources need to be unique within a namespace, but not across namespaces. Namespaces cannot be nested inside one another and each Kubernetes resource can only be in one namespace.

Namespaces are a way to divide cluster resources between multiple users (via [resource quota](https://kubernetes.io/docs/concepts/policy/resource-quotas/)).

In future versions of Kubernetes, objects in the same namespace will have the same access control policies by default.

It is not necessary to use multiple namespaces just to separate slightly different resources, such as different versions of the same software: use labels to distinguish resources within the same namespace.

**There are four namepsaces when a cluster is stood up that are created by default.**

They are:

* **default**
  * This is where all the resources are assumed, unless set otherwise.

* **kube-node-lease**
  * This is the namespace where worker node lease information is kept.

* **kube-public**
  * A namespace readable by all, even those not authenticated. General information is often included in this namespace.

* **kube-system**
  * This namespace contains infrastructure pods.

If you want to see all the resources on your k8s cluster you must use the option `--all-namespaces` with the `kubectl` command.

## Working with Namespaces

In kubectl, namespaces can be passed as command as just `ns`.  Look a these examples:

```bash
kubectl get ns

kubectl create ns mynamespace

kubectl describe ns mynamespace

kubectl get ns/mynamespace -o yaml

kubectl delete ns/mynamespace
```

## API Maturity 

The use of API groups and different versions allows for development to advance without changes to an existing group of APIs. This allows for easier growth and separation of work among separate teams. While there is an attempt to maintain some consistency between API and software versions, they are only indirectly linked.

If you been around games or any software development shop.  you will know that services are released in phases with growing stability and functionality.

Usually the flow is from: Alpha -> Beta -> Stable / Release

* **Alpha** -- An Alpha release, may be buggy and is disabled by default. Features could change or disappear at any time. Only use these features on a test cluster which is often rebuilt.  Often the "bleeding edge"

* **Beta** -- The Beta , has more well-tested code and is enabled by default. It also ensures that, as changes move forward, they will be tested for backwards compatibility between versions. It has not been adopted and tested enough to be called stable. You can expect some bugs and issues. Often the "edge"

* **Stable**  -- Use of the Stable version, denoted by only an integer which may be preceded by the letter v, is for stable APIs.  Use this for production.