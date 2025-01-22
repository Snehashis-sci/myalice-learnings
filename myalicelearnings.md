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

#### Deployment Hands on

Imperative way

```powershell
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> minikube start
😄  minikube v1.35.0 on Microsoft Windows 11 Pro 10.0.26100.2894 Build 26100.2894
✨  Using the docker driver based on existing profile
👍  Starting "minikube" primary control-plane node in "minikube" cluster
🚜  Pulling base image v0.0.46 ...
🔄  Restarting existing docker container for "minikube" ...
❗  Failing to connect to https://registry.k8s.io/ from inside the minikube container
💡  To pull new external images, you may need to configure a proxy: https://minikube.sigs.k8s.io/docs/reference/networking/proxy/
🐳  Preparing Kubernetes v1.32.0 on Docker 27.4.1 ...
🔎  Verifying Kubernetes components...
    ▪ Using image docker.io/kubernetesui/dashboard:v2.7.0
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
    ▪ Using image docker.io/kubernetesui/metrics-scraper:v1.0.8
💡  Some dashboard features require the metrics-server addon. To enable all features please run:

        minikube addons enable metrics-server

🌟  Enabled addons: storage-provisioner, dashboard, default-storageclass

❗  C:\Program Files\Docker\Docker\resources\bin\kubectl.exe is version 1.30.5, which may have incompatibilities with Kubernetes 1.32.0.
    ▪ Want kubectl v1.32.0? Try 'minikube kubectl -- get pods -A'
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> minikube kubectl get pods -A
Error: unknown shorthand flag: 'A' in -A
See 'minikube kubectl --help' for usage.
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> minikube kubectl -- get pods -A
NAMESPACE              NAME                                         READY   STATUS    RESTARTS       AGE
kube-system            coredns-668d6bf9bc-kk77q                     1/1     Running   2 (101s ago)   41h
kube-system            etcd-minikube                                1/1     Running   2 (101s ago)   41h
kube-system            kube-apiserver-minikube                      1/1     Running   2 (101s ago)   41h
kube-system            kube-controller-manager-minikube             1/1     Running   2 (101s ago)   41h
kube-system            kube-proxy-2bjd9                             1/1     Running   2 (101s ago)   41h
kube-system            kube-scheduler-minikube                      1/1     Running   2 (101s ago)   41h
kube-system            storage-provisioner                          1/1     Running   7 (48s ago)    41h
kubernetes-dashboard   dashboard-metrics-scraper-5d59dccf9b-zvslf   1/1     Running   2 (101s ago)   40h
kubernetes-dashboard   kubernetes-dashboard-7779f9b69b-nc8bd        1/1     Running   4 (47s ago)    40h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get pods -A            
NAMESPACE              NAME                                         READY   STATUS    RESTARTS        AGE
kube-system            coredns-668d6bf9bc-kk77q                     1/1     Running   2 (2m27s ago)   41h
kube-system            etcd-minikube                                1/1     Running   2 (2m27s ago)   41h
kube-system            kube-apiserver-minikube                      1/1     Running   2 (2m27s ago)   41h
kube-system            kube-controller-manager-minikube             1/1     Running   2 (2m27s ago)   41h
kube-system            kube-proxy-2bjd9                             1/1     Running   2 (2m27s ago)   41h
kube-system            kube-scheduler-minikube                      1/1     Running   2 (2m27s ago)   41h
kube-system            storage-provisioner                          1/1     Running   7 (94s ago)     41h
kubernetes-dashboard   dashboard-metrics-scraper-5d59dccf9b-zvslf   1/1     Running   2 (2m27s ago)   40h
kubernetes-dashboard   kubernetes-dashboard-7779f9b69b-nc8bd        1/1     Running   4 (93s ago)     40h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get deployments -A
NAMESPACE              NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
kube-system            coredns                     1/1     1            1           41h
kubernetes-dashboard   dashboard-metrics-scraper   1/1     1            1           40h
kubernetes-dashboard   kubernetes-dashboard        1/1     1            1           40h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl apply -f https://k8s.io/examples/controllers/nginx-deployment.yaml
deployment.apps/nginx-deployment created
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get deployments -A                                             
NAMESPACE              NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
default                nginx-deployment            0/3     3            0           9s
kube-system            coredns                     1/1     1            1           41h
kubernetes-dashboard   dashboard-metrics-scraper   1/1     1            1           40h
kubernetes-dashboard   kubernetes-dashboard        1/1     1            1           40h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl rollout status deployment/nginx-deployment
deployment "nginx-deployment" successfully rolled out
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get deployments -A
NAMESPACE              NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
default                nginx-deployment            3/3     3            3           2m16s
kube-system            coredns                     1/1     1            1           41h
kubernetes-dashboard   dashboard-metrics-scraper   1/1     1            1           40h
kubernetes-dashboard   kubernetes-dashboard        1/1     1            1           40h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get rs
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-647677fc66   3         3         3       4m14s
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get pods --show-labels
NAME                                READY   STATUS    RESTARTS   AGE     LABELS
nginx-deployment-647677fc66-9q6wr   1/1     Running   0          5m46s   app=nginx,pod-template-hash=647677fc66
nginx-deployment-647677fc66-l2gsw   1/1     Running   0          5m46s   app=nginx,pod-template-hash=647677fc66
nginx-deployment-647677fc66-z6rp5   1/1     Running   0          5m46s   app=nginx,pod-template-hash=647677fc66
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl describe deployments
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Mon, 20 Jan 2025 11:24:45 +0600
Labels:                 app=nginx
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:         nginx:1.14.2
    Port:          80/TCP
    Host Port:     0/TCP
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-deployment-647677fc66 (3/3 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  16m   deployment-controller  Scaled up replica set nginx-deployment-647677fc66 from 0 to 3
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl set image deployment/nginx-deployment nginx=nginx:1.16.1
deployment.apps/nginx-deployment image updated
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl rollout status deployment/nginx-deployment
Waiting for deployment "nginx-deployment" rollout to finish: 2 out of 3 new replicas have been updated...
Waiting for deployment "nginx-deployment" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "nginx-deployment" rollout to finish: 1 old replicas are pending termination...
deployment "nginx-deployment" successfully rolled out
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get deployments -A
NAMESPACE              NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
default                nginx-deployment            3/3     3            3           18m
kube-system            coredns                     1/1     1            1           41h
kubernetes-dashboard   dashboard-metrics-scraper   1/1     1            1           41h
kubernetes-dashboard   kubernetes-dashboard        1/1     1            1           41h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get rs
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-647677fc66   0         0         0       18m
nginx-deployment-8d94c585f    3         3         3       91s
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-8d94c585f-8brr4   1/1     Running   0          3m29s
nginx-deployment-8d94c585f-hdll9   1/1     Running   0          2m53s
nginx-deployment-8d94c585f-pb75l   1/1     Running   0          2m55s
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl rollout history deployment/nginx-deployment
deployment.apps/nginx-deployment 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>

PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl describe deployments
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Mon, 20 Jan 2025 11:24:45 +0600
Labels:                 app=nginx
Annotations:            deployment.kubernetes.io/revision: 2
Selector:               app=nginx
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:         nginx:1.16.1
    Port:          80/TCP
    Host Port:     0/TCP
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  nginx-deployment-647677fc66 (0/0 replicas created)
NewReplicaSet:   nginx-deployment-8d94c585f (3/3 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  28m   deployment-controller  Scaled up replica set nginx-deployment-647677fc66 from 0 to 3
  Normal  ScalingReplicaSet  11m   deployment-controller  Scaled up replica set nginx-deployment-8d94c585f from 0 to 1
  Normal  ScalingReplicaSet  11m   deployment-controller  Scaled down replica set nginx-deployment-647677fc66 from 3 to 2       
  Normal  ScalingReplicaSet  11m   deployment-controller  Scaled up replica set nginx-deployment-8d94c585f from 1 to 2
  Normal  ScalingReplicaSet  11m   deployment-controller  Scaled down replica set nginx-deployment-647677fc66 from 2 to 1       
  Normal  ScalingReplicaSet  11m   deployment-controller  Scaled up replica set nginx-deployment-8d94c585f from 2 to 3
  Normal  ScalingReplicaSet  11m   deployment-controller  Scaled down replica set nginx-deployment-647677fc66 from 1 to 0       
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl version
Client Version: v1.30.5
Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
Server Version: v1.32.0
WARNING: version difference between client (1.30) and server (1.32) exceeds the supported minor version skew of +/-1
```
* Declarative way

Below is the example of a nginx-deployment yaml file

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

```
Power shell command right below

```powershell
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl apply -f nginx-deployment.yaml
deployment.apps/nginx-deployment created
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get deployments -A
NAMESPACE              NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
default                nginx-deployment            3/3     3            3           8s
kube-system            coredns                     1/1     1            1           45h
kube-system            metrics-server              1/1     1            1           99m
kubernetes-dashboard   dashboard-metrics-scraper   1/1     1            1           44h
kubernetes-dashboard   kubernetes-dashboard        1/1     1            1           44h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl rollout status deployment/nginx-deployment
deployment "nginx-deployment" successfully rolled out
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get rs            
NAME                         DESIRED   CURRENT   READY   AGE
nginx-deployment-8d94c585f   3         3         3       79s
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-8d94c585f-bdgqg   1/1     Running   0          2m10s
nginx-deployment-8d94c585f-clrjk   1/1     Running   0          2m10s
nginx-deployment-8d94c585f-tshdw   1/1     Running   0          2m10s
```

#### Run and expose php-apache server

Firstly run this command to enable metric-srver

```powershell
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> minikube addons enable metrics-server
💡  metrics-server is an addon maintained by Kubernetes. For any concerns contact minikube on GitHub.
You can view the list of minikube maintainers at: https://github.com/kubernetes/minikube/blob/master/OWNERS
    ▪ Using image registry.k8s.io/metrics-server/metrics-server:v0.7.2
🌟  The 'metrics-server' addon is enabled
```

here's an example of hpa in yaml file

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: php-apache
spec:
  selector:
    matchLabels:
      run: php-apache
  template:
    metadata:
      labels:
        run: php-apache
    spec:
      containers:
      - name: php-apache
        image: registry.k8s.io/hpa-example
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 500m
          requests:
            cpu: 200m
---
apiVersion: v1
kind: Service
metadata:
  name: php-apache
  labels:
    run: php-apache
spec:
  ports:
  - port: 80
  selector:
    run: php-apache

```

Here's the command line

```powershell
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl apply -f php-apache.yaml
deployment.apps/php-apache created
service/php-apache unchanged
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get service
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP   47h
php-apache   ClusterIP   10.102.135.128   <none>        80/TCP    3h42m
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl describe deployment      
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Mon, 20 Jan 2025 15:25:02 +0600
Labels:                 app=nginx
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:         nginx:1.16.1
    Port:          80/TCP
    Host Port:     0/TCP
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-deployment-8d94c585f (3/3 replicas created)
Events:          <none>


Name:                   php-apache
Namespace:              default
CreationTimestamp:      Mon, 20 Jan 2025 17:29:21 +0600
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               run=php-apache
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  run=php-apache
  Containers:
   php-apache:
    Image:      registry.k8s.io/hpa-example
    Port:       80/TCP
    Host Port:  0/TCP
    Limits:
      cpu:  500m
    Requests:
      cpu:         200m
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   php-apache-6487c65df8 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  12m   deployment-controller  Scaled up replica set php-apache-6487c65df8 from 0 to 1

```
Creating HorizontalPodAutoscaler:

```powershell
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
horizontalpodautoscaler.autoscaling/php-apache autoscaled 
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get hpa                                        
NAME         REFERENCE               TARGETS       MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   cpu: 0%/50%   1         10        1          4h7m
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl describe deployment                            
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Mon, 20 Jan 2025 15:25:02 +0600
Labels:                 app=nginx
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:         nginx:1.16.1
    Port:          80/TCP
    Host Port:     0/TCP
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-deployment-8d94c585f (3/3 replicas created)
Events:          <none>


Name:                   php-apache
Namespace:              default
CreationTimestamp:      Mon, 20 Jan 2025 17:29:21 +0600
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               run=php-apache
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  run=php-apache
  Containers:
   php-apache:
    Image:      registry.k8s.io/hpa-example
    Port:       80/TCP
    Host Port:  0/TCP
    Limits:
      cpu:  500m
    Requests:
      cpu:         200m
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   php-apache-6487c65df8 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  31m   deployment-controller  Scaled up replica set php-apache-6487c65df8 from 0 to 1
```

Algorithm for desiredReplicas:

`desiredReplicas = ceil[currentReplicas * ( currentMetricValue / desiredMetricValue )]`

where currentMetricValue is the current cpu usage and desiredMetricValue is desired cpu usage.

Volume mounting!!??

Things to cover:  
* how deployment, statefulset, daemonset mounts volumes, gets values from configmap and secrets

*  how node-affinity, pod-affinity and anti-affinity works

* how taint toleration works

* run a frontend, backend and database in minikube, using node port expose the frontend and backend.

* how to add/remove taints to/from nodes

* Check out pod disruption budget, network policy, RBAC, service account.

* Also check horizontal pod autoscaler, sidecar, kubernetes job, cronjob

```powershell
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get rs
NAME                         DESIRED   CURRENT   READY   AGE
nginx-deployment-8d94c585f   3         3         3       3h38m
php-apache-6487c65df8        1         1         1       94m
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl rollout restart deploy nginx-deployment
deployment.apps/nginx-deployment restarted
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get rs
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-66869844b7   3         3         2       8s
nginx-deployment-8d94c585f    1         1         1       3h40m
php-apache-6487c65df8         1         1         1       96m
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-66869844b7-5xspl   1/1     Running   0          45s
nginx-deployment-66869844b7-fgkks   1/1     Running   0          42s
nginx-deployment-66869844b7-kvvk7   1/1     Running   0          39s
php-apache-6487c65df8-sd4m6         1/1     Running   0          97m
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get rs  
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-66869844b7   3         3         3       93s
nginx-deployment-8d94c585f    0         0         0       3h42m
php-apache-6487c65df8         1         1         1       97m
```

21/01/2025 [create a namespace using yaml file, create a deployment yaml using previously created namespace, create a file for service in that namespace and the service should forward traffic to the deployment previously created,
use port forward expose the service, check from browser that the service is accessible.]
```powershell
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> minikube start
😄  minikube v1.35.0 on Microsoft Windows 11 Pro 10.0.26100.2894 Build 26100.2894
✨  Using the docker driver based on existing profile

💣  Exiting due to PROVIDER_DOCKER_VERSION_EXIT_1: "docker version --format <no value>-<no value>:<no value>" exit status 1: error during connect: Get "http://%2F%2F.%2Fpipe%2FdockerDesktopLinuxEngine/v1.47/version": open //./pipe/dockerDesktopLinuxEngine: The system cannot find the file specified.
📘  Documentation: https://minikube.sigs.k8s.io/docs/drivers/docker/

PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> docker start
"docker start" requires at least 1 argument.
See 'docker start --help'.

Usage:  docker start [OPTIONS] CONTAINER [CONTAINER...]

Start one or more stopped containers
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> docker start --help

Usage:  docker start [OPTIONS] CONTAINER [CONTAINER...]

Start one or more stopped containers

Aliases:
  docker container start, docker start

Options:
  -a, --attach                  Attach STDOUT/STDERR and forward signals
      --checkpoint string       Restore from this checkpoint
      --checkpoint-dir string   Use a custom checkpoint storage directory
      --detach-keys string      Override the key sequence for detaching a
                                container
  -i, --interactive             Attach container's STDIN
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> minikube start     
😄  minikube v1.35.0 on Microsoft Windows 11 Pro 10.0.26100.2894 Build 26100.2894
✨  Using the docker driver based on existing profile
👍  Starting "minikube" primary control-plane node in "minikube" cluster
🚜  Pulling base image v0.0.46 ...
🔄  Restarting existing docker container for "minikube" ...
❗  Failing to connect to https://registry.k8s.io/ from inside the minikube container
💡  To pull new external images, you may need to configure a proxy: https://minikube.sigs.k8s.io/docs/reference/networking/proxy/
🐳  Preparing Kubernetes v1.32.0 on Docker 27.4.1 ...
🔎  Verifying Kubernetes components...
    ▪ Using image registry.k8s.io/metrics-server/metrics-server:v0.7.2
    ▪ Using image docker.io/kubernetesui/dashboard:v2.7.0
    ▪ Using image docker.io/kubernetesui/metrics-scraper:v1.0.8
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
💡  Some dashboard features require the metrics-server addon. To enable all features please run:

        minikube addons enable metrics-server

🌟  Enabled addons: metrics-server, storage-provisioner, default-storageclass, dashboard

❗  C:\Program Files\Docker\Docker\resources\bin\kubectl.exe is version 1.30.5, which may have incompatibilities with Kubernetes 1.32.0.
    ▪ Want kubectl v1.32.0? Try 'minikube kubectl -- get pods -A'
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> minikube addons enable metrics-server
💡  metrics-server is an addon maintained by Kubernetes. For any concerns contact minikube on GitHub.
You can view the list of minikube maintainers at: https://github.com/kubernetes/minikube/blob/master/OWNERS
    ▪ Using image registry.k8s.io/metrics-server/metrics-server:v0.7.2
🌟  The 'metrics-server' addon is enabled
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> minikube kubectl -- get pods -A
NAMESPACE              NAME                                         READY   STATUS    RESTARTS       AGE
default                nginx-deployment-66869844b7-5xspl            1/1     Running   1 (2m4s ago)   17h
default                nginx-deployment-66869844b7-fgkks            1/1     Running   1 (2m4s ago)   17h
default                nginx-deployment-66869844b7-kvvk7            1/1     Running   1 (2m4s ago)   17h
default                php-apache-6487c65df8-sd4m6                  1/1     Running   1 (2m4s ago)   18h
kube-system            coredns-668d6bf9bc-kk77q                     1/1     Running   3 (2m4s ago)   2d18h
kube-system            etcd-minikube                                1/1     Running   3 (2m4s ago)   2d18h
kube-system            kube-apiserver-minikube                      1/1     Running   3 (2m4s ago)   2d18h
kube-system            kube-controller-manager-minikube             1/1     Running   3 (2m4s ago)   2d18h
kube-system            kube-proxy-2bjd9                             1/1     Running   3 (2m4s ago)   2d18h
kube-system            kube-scheduler-minikube                      1/1     Running   3 (2m4s ago)   2d18h
kube-system            metrics-server-7fbb699795-c65mc              1/1     Running   1 (2m4s ago)   22h
kube-system            storage-provisioner                          1/1     Running   9 (58s ago)    2d18h
kubernetes-dashboard   dashboard-metrics-scraper-5d59dccf9b-zvslf   1/1     Running   3 (2m4s ago)   2d17h
kubernetes-dashboard   kubernetes-dashboard-7779f9b69b-nc8bd        1/1     Running   6 (53s ago)    2d17h
production             nginx-deployment-8d94c585f-6v9dj             1/1     Running   1 (2m4s ago)   16h
production             nginx-deployment-8d94c585f-f7vdr             1/1     Running   1 (2m4s ago)   16h
production             nginx-deployment-8d94c585f-j65h4             1/1     Running   1 (2m4s ago)   16h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get deployment -A
NAMESPACE              NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
default                nginx-deployment            3/3     3            3           20h
default                php-apache                  1/1     1            1           18h
kube-system            coredns                     1/1     1            1           2d18h
kube-system            metrics-server              1/1     1            1           22h
kubernetes-dashboard   dashboard-metrics-scraper   1/1     1            1           2d17h
kubernetes-dashboard   kubernetes-dashboard        1/1     1            1           2d17h
production             nginx-deployment            3/3     3            3           16h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl edit -f nginx-deployment.yaml 
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl apply -f nginx-deployment.yaml
deployment.apps/nginx-deployment configured
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get namespace                 
NAME                   STATUS   AGE
default                Active   2d18h
kube-node-lease        Active   2d18h
kube-public            Active   2d18h
kube-system            Active   2d18h
kubernetes-dashboard   Active   2d17h
production             Active   17h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl apply -f service.yaml         
service/app-service created
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl apply -f service.yaml
service/nginx-service created
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> ping localhost:80
Ping request could not find host localhost:80. Please check the name and try again.
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl delete deployment nginx-deployment default
deployment.apps "nginx-deployment" deleted
Error from server (NotFound): deployments.apps "default" not found
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get deployment
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
php-apache   1/1     1            1           19h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get deployment -A
NAMESPACE              NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
default                php-apache                  1/1     1            1           19h
kube-system            coredns                     1/1     1            1           2d18h
kube-system            metrics-server              1/1     1            1           22h
kubernetes-dashboard   dashboard-metrics-scraper   1/1     1            1           2d18h
kubernetes-dashboard   kubernetes-dashboard        1/1     1            1           2d18h
production             nginx-deployment            3/3     3            3           17h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl delete deployment php-apache default      
deployment.apps "php-apache" deleted
Error from server (NotFound): deployments.apps "default" not found
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get deployment -A
NAMESPACE              NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
kube-system            coredns                     1/1     1            1           2d18h
kube-system            metrics-server              1/1     1            1           22h
kubernetes-dashboard   dashboard-metrics-scraper   1/1     1            1           2d18h
kubernetes-dashboard   kubernetes-dashboard        1/1     1            1           2d18h
production             nginx-deployment            3/3     3            3           17h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl describe service nginx-service
Error from server (NotFound): services "nginx-service" not found
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get all          
NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP   2d18h
service/php-apache   ClusterIP   10.102.135.128   <none>        80/TCP    23h

NAME                                             REFERENCE               TARGETS       MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/php-apache   Deployment/php-apache   cpu: 0%/50%   1         10        1          23h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get deployment -A
NAMESPACE              NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
kube-system            coredns                     1/1     1            1           2d19h
kube-system            metrics-server              1/1     1            1           23h
kubernetes-dashboard   dashboard-metrics-scraper   1/1     1            1           2d18h
kubernetes-dashboard   kubernetes-dashboard        1/1     1            1           2d18h
production             nginx-deployment            3/3     3            3           17h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get pod
No resources found in default namespace.
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get namespace 
NAME                   STATUS   AGE
default                Active   2d19h
kube-node-lease        Active   2d19h
kube-public            Active   2d19h
kube-system            Active   2d19h
kubernetes-dashboard   Active   2d18h
production             Active   17h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get service
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP   2d19h
php-apache   ClusterIP   10.102.135.128   <none>        80/TCP    23h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl delete service php-apache
service "php-apache" deleted
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl apply -f service.yaml
service/nginx-service unchanged
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get service
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   2d19h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl start service nginx-service
error: unknown command "start" for "kubectl"
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl apply -f nginx-deployment.yaml
deployment.apps/nginx-deployment unchanged
service/nginx-service unchanged
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get service
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   2d19h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get pod
No resources found in default namespace.
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   2d19h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl apply -f nginx-deployment.yaml
deployment.apps/nginx-deployment unchanged
service/nginx-service unchanged
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get pod
No resources found in default namespace.
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get namespace
NAME                   STATUS   AGE
default                Active   2d19h
kube-node-lease        Active   2d19h
kube-public            Active   2d19h
kube-system            Active   2d19h
kubernetes-dashboard   Active   2d19h
production             Active   18h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl -n production
kubectl controls the Kubernetes cluster manager.

 Find more information at: https://kubernetes.io/docs/reference/kubectl/

Basic Commands (Beginner):
  create          Create a resource from a file or from stdin
  expose          Take a replication controller, service, deployment or pod and expose it as a new Kubernetes service
  run             Run a particular image on the cluster
  set             Set specific features on objects

Basic Commands (Intermediate):
  explain         Get documentation for a resource
  get             Display one or many resources
  edit            Edit a resource on the server
  delete          Delete resources by file names, stdin, resources and names, or by resources and label selector

Deploy Commands:
  rollout         Manage the rollout of a resource
  scale           Set a new size for a deployment, replica set, or replication controller
  autoscale       Auto-scale a deployment, replica set, stateful set, or replication controller

Cluster Management Commands:
  certificate     Modify certificate resources
  cluster-info    Display cluster information
  top             Display resource (CPU/memory) usage
  cordon          Mark node as unschedulable
  uncordon        Mark node as schedulable
  drain           Drain node in preparation for maintenance
  taint           Update the taints on one or more nodes

Troubleshooting and Debugging Commands:
  describe        Show details of a specific resource or group of resources
  logs            Print the logs for a container in a pod
  attach          Attach to a running container
  exec            Execute a command in a container
  port-forward    Forward one or more local ports to a pod
  proxy           Run a proxy to the Kubernetes API server
  cp              Copy files and directories to and from containers
  auth            Inspect authorization
  debug           Create debugging sessions for troubleshooting workloads and nodes
  events          List events

Advanced Commands:
  diff            Diff the live version against a would-be applied version
  apply           Apply a configuration to a resource by file name or stdin
  patch           Update fields of a resource
  replace         Replace a resource by file name or stdin
  wait            Experimental: Wait for a specific condition on one or many resources
  kustomize       Build a kustomization target from a directory or URL

Settings Commands:
  label           Update the labels on a resource
  annotate        Update the annotations on a resource
  completion      Output shell completion code for the specified shell (bash, zsh, fish, or powershell)

Subcommands provided by plugins:

Other Commands:
  api-resources   Print the supported API resources on the server
  api-versions    Print the supported API versions on the server, in the form of "group/version"
  config          Modify kubeconfig files
  plugin          Provides utilities for interacting with plugins
  version         Print the client and server version information

Usage:
  kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl -n production get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7f6cd6c45f-cwphf   1/1     Running   0          68m
nginx-deployment-7f6cd6c45f-qvnjw   1/1     Running   0          68m
nginx-deployment-7f6cd6c45f-vlzqv   1/1     Running   0          67m
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl -n production get pod,svc 
NAME                                    READY   STATUS    RESTARTS   AGE
NAME                                    READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-7f6cd6c45f-cwphf   1/1     Running   0          68m
pod/nginx-deployment-7f6cd6c45f-qvnjw   1/1     Running   0          68m
pod/nginx-deployment-7f6cd6c45f-vlzqv   1/1     Running   0          68m

NAME                    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/app-service     ClusterIP   10.110.200.207   <none>        80/TCP    67m
service/nginx-service   ClusterIP   10.105.232.252   <n> ^C                                                                     
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl -n production get pod,svc
NAME                                    READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-7f6cd6c45f-cwphf   1/1     Running   0          77m
pod/nginx-deployment-7f6cd6c45f-qvnjw   1/1     Running   0          77m
pod/nginx-deployment-7f6cd6c45f-vlzqv   1/1     Running   0          77m

NAME                    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/app-service     ClusterIP   10.110.200.207   <none>        80/TCP    76m
service/nginx-service   ClusterIP   10.105.232.252   <none>        80/TCP    72m
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get pod nginx-deployment-7f6cd6c45f-cwphf --template='{{(index (index .spec.containers 0).ports 0).containerPort}}{{"\n"}}'
Error from server (NotFound): pods "nginx-deployment-7f6cd6c45f-cwphf" not found
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get pods
No resources found in default namespace.
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get namespace
NAME                   STATUS   AGE
default                Active   2d19h
kube-node-lease        Active   2d19h
kube-public            Active   2d19h
kube-system            Active   2d19h
kubernetes-dashboard   Active   2d19h
production             Active   18h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get svc      
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   2d19h
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl -n production get svc
NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
app-service     ClusterIP   10.110.200.207   <none>        80/TCP    86m
nginx-service   ClusterIP   10.105.232.252   <none>        80/TCP    83m
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl -n production get service nginx-service --template='{{(index (index .spec.containers 0).ports 0).containerPort}}{{"\n"}}'
error: error parsing template {{(index (index .spec.containers 0).ports 0).containerPort}}{{\n}}, template: output:1: unexpected "\\" in command
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl port-forward service/nginx-service 80:8080
Error from server (NotFound): services "nginx-service" not found
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl -n production port-forward service/nginx-service 80:8080
error: Service nginx-service does not have a service port 8080
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl -n production port-forward service/nginx-service 80:80  
Forwarding from 127.0.0.1:80 -> 8080
Forwarding from [::1]:80 -> 8080
Handling connection for 80
E0121 14:03:14.880669   22344 portforward.go:413] an error occurred forwarding 80 -> 8080: error forwarding port 8080 to pod f5407e9f2054ffe0c649c8b37d69f9e620f347a565cbbcb56f3ae0926c3e4e9d, uid : exit status 1: 2025/01/21 08:03:14 socat[27532] E connect(5, AF=2 127.0.0.1:8080, 16): Connection refused
error: lost connection to pod
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl -n production get pods       
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7f6cd6c45f-cwphf   1/1     Running   0          104m
nginx-deployment-7f6cd6c45f-qvnjw   1/1     Running   0          104m
nginx-deployment-7f6cd6c45f-vlzqv   1/1     Running   0          104m
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl -n production describe pods nginx-deployment-7f6cd6c45f-cwphf
Name:             nginx-deployment-7f6cd6c45f-cwphf
Namespace:        production
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Tue, 21 Jan 2025 12:26:50 +0600
Labels:           app=nginx
                  pod-template-hash=7f6cd6c45f
Annotations:      <none>
Status:           Running
IP:               10.244.0.41
IPs:
  IP:           10.244.0.41
Controlled By:  ReplicaSet/nginx-deployment-7f6cd6c45f
Containers:
  nginx:
    Container ID:   docker://3c0f9f7de577f8d270095ed441dc4eb19286bc5d924fb71d82c8a95eb416ad2d
    Image:          nginx:1.16.1
    Image ID:       docker-pullable://nginx@sha256:d20aa6d1cae56fd17cd458f4807e0de462caf2336f0b70b5eeb69fcaaf30dd9c
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Tue, 21 Jan 2025 12:26:52 +0600
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-jhgm8 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       True
  ContainersReady             True
  PodScheduled                True
Volumes:
  kube-api-access-jhgm8:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:                      <none>
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl -n production port-forward service/nginx-service 80:8080
error: Service nginx-service does not have a service portkubectl apply -f nginx-deployment.yaml                 
deployment.apps/nginx-deployment configured
service/nginx-service configured
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl get pods
No resources found in default namespace.
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl -n production get pods
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-8d94c585f-2zb68   1/1     Running   0          34s
nginx-deployment-8d94c585f-9n2k8   1/1     Running   0          29s
nginx-deployment-8d94c585f-lcftw   1/1     Running   0          32s
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl -n production describe pods nginx-deployment-8d94c585f-2zb68
Name:             nginx-deployment-8d94c585f-2zb68
Namespace:        production
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Tue, 21 Jan 2025 15:59:10 +0600
Labels:           app=nginx
                  pod-template-hash=8d94c585f
Annotations:      <none>
Status:           Running
IP:               10.244.0.43
IPs:
  IP:           10.244.0.43
Controlled By:  ReplicaSet/nginx-deployment-8d94c585f
Containers:
  nginx:
    Container ID:   docker://1c1f7d998335da91a4d245156137cc8675dd50ddde3c311c86e20895c9274a51
    Image:          nginx:1.16.1
    Image ID:       docker-pullable://nginx@sha256:d20aa6d1cae56fd17cd458f4807e0de462caf2336f0b70b5eeb69fcaaf30dd9c
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Tue, 21 Jan 2025 15:59:11 +0600
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-72gcd (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       True
  ContainersReady             True
  PodScheduled                True
Volumes:
  kube-api-access-72gcd:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  91s   default-scheduler  Successfully assigned production/nginx-deployment-8d94c585f-2zb68 to minikube
  Normal  Pulled     90s   kubelet            Container image "nginx:1.16.1" already present on machine
  Normal  Created    90s   kubelet            Created container: nginx
  Normal  Started    90s   kubelet            Started container nginx
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl -n production port-forward service/nginx-service 82:80
error: Service nginx-service does not have a service port 80
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> kubectl -n production port-forward service/nginx-service 80:82
Forwarding from 127.0.0.1:80 -> 80
Forwarding from [::1]:80 -> 80
Handling connection for 80
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings> minikube stop
✋  Stopping node "minikube"  ...
🛑  Powering off "minikube" via SSH ...
🛑  1 node stopped.
```

why theres 2 replicas but 3 pods???