# Kubernetes

Kubernetes is a portable, extensible, open source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation. It has a large, rapidly growing ecosystem. Kubernetes services, support, and tools are widely available.

The name Kubernetes originates from Greek, meaning helmsman or pilot. K8s as an abbreviation results from counting the eight letters between the "K" and the "s".

## Why do we need kubernetes?

Containers are a good way to bundle and run your applications. In a production environment, you need to manage the containers that run the applications and ensure that there is no downtime. For example, if a container goes down, another container needs to start. Wouldn't it be easier if this behavior was handled by a system?

That's how Kubernetes comes to the rescue! Kubernetes provides you with a framework to run distributed systems resiliently. It takes care of scaling and failover for your application, provides deployment patterns, and more. For example: Kubernetes can easily manage a canary deployment for your system.

Kubernetes provides you with:

* **Service discovery and load balancing** Kubernetes can expose a container using the DNS name or using their own IP address. If traffic to a container is high, Kubernetes is able to load balance and distribute the network traffic so that the deployment is stable.
* **Storage orchestration** Kubernetes allows you to automatically mount a storage system of your choice, such as local storages, public cloud providers, and more.
* **Automated rollouts and rollbacks** You can describe the desired state for your deployed containers using Kubernetes, and it can change the actual state to the desired state at a controlled rate. For example, you can automate Kubernetes to create new containers for your deployment, remove existing containers and adopt all their resources to the new container.
* **Automatic bin packing** You provide Kubernetes with a cluster of nodes that it can use to run containerized tasks. You tell Kubernetes how much CPU and memory (RAM) each container needs. Kubernetes can fit containers onto your nodes to make the best use of your resources.
* **Self-healing** Kubernetes restarts containers that fail, replaces containers, kills containers that don't respond to your user-defined health check, and doesn't advertise them to clients until they are ready to serve.
* **Secret and configuration management** Kubernetes lets you store and manage sensitive information, such as passwords, OAuth tokens, and SSH keys. You can deploy and update secrets and application configuration without rebuilding your container images, and without exposing secrets in your stack configuration.
* **Batch execution** In addition to services, Kubernetes can manage your batch and CI workloads, replacing containers that fail, if desired.
* **Horizontal scaling** Scale your application up and down with a simple command, with a UI, or automatically based on CPU usage.
* **IPv4/IPv6 dual-stack** Allocation of IPv4 and IPv6 addresses to Pods and Services
* **Designed for extensibility** Add features to your Kubernetes cluster without changing upstream source code.

## Kubernetes Core Components

![picture alt](https://kubernetes.io/images/docs/components-of-kubernetes.svg "Kubernetes Core Components Diagram")

The components of a Kubernetes cluster

### Core Components

A kubernetes cluster contains of a control plane and one or more worker nodes. Here's a brief overview of the main components:

#### Control Plane components

These manage the overall state of the cluster:

* kube-apiserver: The core component server that exposes the kubernetes HTTP API.

* etcd: Consistent and highly-available key value store for all API server data.

* kube-scheduler: Looks for pods not yet bound to a node and assigns each pod to a suitable node.

* kube-controller-manager: Runs controllers to implement Kubernetes API behavior.

* cloud-controller-manager: Integrates with underlying cloud provider(s).

#### Node Components

Run on every node, maintaining running pods and providing the Kubernetes runtime environment:
 
* kubelet: Ensures that Pods are running, including their containers.

* kube-proxy: Maintains network rules on nodes to implement Services.

* Container runtime: Software responsible for running containers. <!---this is not clear yet -->

### Kubernetes Object Management

The kubectl command-line tool supports several different ways to create and manage Kubernetes objects.

Three kinds of techniques are used: 
* Imperative commands
* Imperative object configuration 
* Declarative configuration.

#### Imperative commands

When using imperative commands, a user operates directly on live objects in a cluster. The user provides operations to the kubectl command as arguments or flags.
This is the recommended way to get started or to run a one-off task in a cluster. Because this technique operates directly on live objects, it provides no history of previous configurations.

Examples

Run an instance of the nginx container by creating a Deployment object:

`kubectl create deployment nginx --image nginx`

#### Imperative object configuration

In imperative object configuration, the kubectl command specifies the operation (create, replace, etc.), optional flags and at least one file name. The file specified must contain a full definition of the object in YAML or JSON format.

Examples

Create the objects defined in a configuration file:

`kubectl create -f nginx.yaml`

Delete the objects defined in two configuration files:

`kubectl delete -f nginx.yaml -f redis.yaml`

Update the objects defined in a configuration file by overwriting the live configuration:

`kubectl replace -f nginx.yaml`

#### Declarative object configuration

When using declarative object configuration, a user operates on object configuration files stored locally, however the user does not define the operations to be taken on the files. Create, update, and delete operations are automatically detected per-object by kubectl. This enables working on directories, where different operations might be needed for different objects.

Examples

Process all object configuration files in the configs directory, and create or patch the live objects. You can first diff to see what changes are going to be made, and then apply:

```shell
kubectl diff -f configs/
kubectl apply -f configs/
```

Recursively process directories:

```shell
kubectl diff -R -f configs/
kubectl apply -R -f configs/
```

#### Workload Management

Built in apis for managing workloads:

* Deployment - manages stateless application on the cluster where aany pod in the deployment can be interrchangeable.
* Statefulset - lets us manage more than one  pods running in the same application code. it makes a link between itspos and their pv. each pod associated with a pv. if one fails, k8s makes a replacement that is connencted to the same pv.
* Daemonset - we e use it when a node level service has to rn on the node.it performs roles similar to system daemon on a unix system.(still not clear, what is node level service?)


Cronjobs - performs regular scheduled actions such as backups or report generation. spec.schedule field is denoted by five stars (*****) where the stars defined by minute(0-59), hour(0-23), day of the month(1-31), number of the month(1-12) and day of the week(0-6) from left to right. for example; 0 9 * * 2 means the task scheduled to run weekly on tuesday at 9am.

ReplicaSet- A ReplicaSet is to maintain a stable set of replica Pods running at any given time. we define a Deployment and that Deployment manage ReplicaSets automatically.

Volume mounting!!??