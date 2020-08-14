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
