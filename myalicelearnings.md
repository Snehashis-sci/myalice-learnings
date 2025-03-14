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

-Why theres 2 replicas but 3 pods???

the deployment has 2 replicas, meaning there are 2 identical pod instances running. However, the total number of pods is 3. This could happen if one of the pods is in a transitional state, such as being created, terminated, or restarted. Kubernetes will ensure that the desired number of 2 replicas is maintained, even if one of the pods is temporarily in a non-running state.

The discrepancy between the number of replicas and the number of pods is normal and can occur due to various reasons, such as pod failures, scaling operations, or deployment updates. Kubernetes will automatically manage the pod lifecycle to ensure the desired number of replicas is maintained.

#### StatefulSet

* terminationGracePeriodSeconds

* volumeClaimTemplates

Using configmap in statefulset--

Create a ConfigMap:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-configmap
data:
  config-key: config-value

```
Use the ConfigMap in StatefulSet:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: example-statefulset
spec:
  serviceName: "example"
  replicas: 3
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - name: example-container
        image: example-image
        volumeMounts:
        - name: config-volume
          mountPath: /etc/config
      volumes:
      - name: config-volume
        configMap:
          name: example-configmap

```

Using Secrets--

Create a Secret:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: example-secret
type: Opaque
data:
  secret-key: c2VjcmV0LXZhbHVl # Base64 encoded value

```

Use the Secret in StatefulSet:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: example-statefulset
spec:
  serviceName: "example"
  replicas: 3
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - name: example-container
        image: example-image
        volumeMounts:
        - name: secret-volume
          mountPath: /etc/secret
      volumes:
      - name: secret-volume
        secret:
          secretName: example-secret

```
#### DaemonSet

* tolerations

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      tolerations:
      # these tolerations are to have the daemonset runnable on control plane nodes
      # remove them if your control plane nodes should not run pods
      - key: node-role.kubernetes.io/control-plane
        operator: Exists
        effect: NoSchedule
      - key: node-role.kubernetes.io/master
        operator: Exists
        effect: NoSchedule
      containers:
      - name: fluentd-elasticsearch
        image: quay.io/fluentd_elasticsearch/fluentd:v2.5.2
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
      # it may be desirable to set a high priority class to ensure that a DaemonSet Pod
      # preempts running Pods
      # priorityClassName: important
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log

```


#### Kustomize

Today i have created an application properties and env file first and then a kustomization file for it and checked it through `kubectl kustomize ./`. both of the files were referred correctly. It is better to edit these files manually as there can be encoding issue while creating these files from powershell. and later on, i have generated password file and used secretgenerator and linked it to a new deployment file. Ran the `kubectl kustomize ./`. Everything went smooth. 

```Powershell
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings\kustomize> kubectl kustomize ./
apiVersion: v1
data:
  FOO: bar
kind: ConfigMap
metadata:
  name: example-configmap-1-59m54fbgh2
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings\kustomize> kubectl kustomize ./
apiVersion: v1
binaryData:
  application.properties: //5GAE8ATwA9AEIAYQByAA0ACgA=
kind: ConfigMap
metadata:
  name: example-configmap-1-d9fcctmmc2
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: my-app
  name: my-app
spec:
  selector:
    matchLabels:
      app: my-app
  template:
      labels:
        app: my-app
    spec:
      containers:
      - image: my-app
        name: app
        volumeMounts:
        - mountPath: /config
          name: config
      volumes:
      - configMap:
          name: example-configmap-1-d9fcctmmc2
        name: config
PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings\kustomize>

PS C:\Users\Sndevice\Documents\GitHub\myalice-learnings\kustomize> kubectl kustomize ./
apiVersion: v1
binaryData:
  application.properties: //5GAE8ATwA9AEIAYQByAA0ACgA=
kind: ConfigMap
metadata:
  name: example-configmap-1-d9fcctmmc2
---
apiVersion: v1
data:
  password.txt: dXNlcm5hbWU9YWRtaW4NCnBhc3N3b3JkPXNlY3JldA==
kind: Secret
metadata:
  name: example-secret-1-gf8hktg4gm
type: Opaque
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: my-app
  name: my-app
spec:
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - image: my-app
        name: app
        volumeMounts:
        - mountPath: /config
          name: config
      volumes:
      - configMap:
          name: example-configmap-1-d9fcctmmc2
        name: config
```

#### Helm

Helm charts: A bundle of yaml files which can be downloaded and be used for applications. We can also make our own helm charts with helm and push them to the helm repository. 
Sharing helm charts: We can share helm charts. Look it up with `helm search <keyword>`. THeres a helm hub a a public registry where we can find various helm charts to use.
Templating engine: It can be used as a templating engine where we can define a common blueprint and the dynamic values can be replaced with a placeholder. We have to have a template yamnl config file and another value.yaml file to get the values to use it on the template file which can be used for common pods.
Release management: 

Created a sample helloworld helm chart with nginx image and installed it to run.

```powershell
PS C:\Users\Sndevice> helm install myhelloworld helloworld
NAME: myhelloworld
LAST DEPLOYED: Tue Feb  4 15:05:47 2025
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services myhelloworld)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
PS C:\Users\Sndevice> helm list -a
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
myhelloworld    default         1               2025-02-04 15:05:47.8161656 +0600 +06   deployed        helloworld-0.1.0        1.16.0  

PS C:\Users\Sndevice> kubectl get service
NAME             TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
kubernetes       ClusterIP   10.96.0.1        <none>        443/TCP          16d
mongo-service    ClusterIP   10.109.210.174   <none>        27017/TCP        12d
myhelloworld     NodePort    10.109.37.253    <none>        80:31801/TCP     7m7s
webapp-service   NodePort    10.101.192.143   <none>        3000:30200/TCP   12d
```

Changed the service type to nodePort 

```yaml
service:
  # This sets the service type more information can be found here: https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types
  type: NodePort
  # This sets the ports more information can be found here: https://kubernetes.io/docs/concepts/services-networking/service/#field-spec-ports
  port: 80
```

Applied the following cmd line to run on the browser.

`minikube service myhelloworld --url`

5th feb 2025

Today i have followed the helm chart tutorial by Richard Chesterwood. i have used docker desktop with an ubuntu image to get a gitbash and perform my actions. Here i have gone through the overview of helm chart and basic cli commands. then i have created a helm chart of my own named as myownchart. it created a directory with the same name and in that directory i have given other instructions. Following that i have checked out the artifacthub.io which is an helm repo and searched for prometheus chart which is for monitoring my app. It includes grafana as well. I have added the repo to my system and updated it. Installed the prometheus chart and edited the svc file from clusterip to NodePort and added a nodePort to it.  I had to use a service tunnel to view the content on browser. I have gone through these following bash lines.

```bash
Sndevice@Sndevice MINGW64 ~
$ helm create myownchart
Creating myownchart
Sndevice@Sndevice MINGW64 ~
$ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
"prometheus-community" has been added to your repositories

Sndevice@Sndevice MINGW64 ~
$ helm repo list
NAME                    URL
prometheus-community    https://prometheus-community.github.io/helm-charts

Sndevice@Sndevice MINGW64 ~
$ cd myownchart

Sndevice@Sndevice MINGW64 ~/myownchart
$ helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "prometheus-community" chart repository
Update Complete. ⎈Happy Helming!⎈

Sndevice@Sndevice MINGW64 ~/myownchart
$ helm install monitoring prometheus-community/kube-prometheus-stack --version 68.4.5
NAME: monitoring
LAST DEPLOYED: Wed Feb  5 16:38:17 2025
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
kube-prometheus-stack has been installed. Check its status by running:
  kubectl --namespace default get pods -l "release=monitoring"

Get Grafana 'admin' user password by running:

  kubectl --namespace default get secrets prom-grafana -ojsonpath="{.data.admin-password}" | base64 -d ; echo

Access Grafana local instance:

  export POD_NAME=$(kubectl --namespace default get pod -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=monitoring" -oname)
  kubectl --namespace default port-forward $POD_NAME 3000

Visit https://github.com/prometheus-operator/kube-prometheus for instructions on how to create & configure Alertmanager and Prometheus instances using the Operator.

Sndevice@Sndevice MINGW64 ~/myownchart
$ ls
Chart.yaml  charts  templates  values.yaml

Sndevice@Sndevice MINGW64 ~/myownchart
$ ^C

Sndevice@Sndevice MINGW64 ~/myownchart
$ kubectl --namespace default get pods -l "release=monitoring"
NAME                                                   READY   STATUS    RESTARTS   AGE
monitoring-kube-prometheus-operator-7859f9db49-4qbcg   1/1     Running   0          105s
monitoring-kube-state-metrics-56d9ddb9c7-dfswp         1/1     Running   0          105s
monitoring-prometheus-node-exporter-x8gq2              1/1     Running   0          105s

Sndevice@Sndevice MINGW64 ~/myownchart
$ cd charts

Sndevice@Sndevice MINGW64 ~/myownchart/charts
$ ls

Sndevice@Sndevice MINGW64 ~/myownchart/charts
$ cd .

Sndevice@Sndevice MINGW64 ~/myownchart/charts
$ cd ..

Sndevice@Sndevice MINGW64 ~/myownchart
$ helm list
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHARTART                ART                             APP VERSION
monitoring      default         1               2025-02-05 16:38:17.9310121 +0600 +06   deployed        kube-prometheus-stack-68.4.5      v0.79.2

Sndevice@Sndevice MINGW64 ~/myownchart
$ helm list
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                             APP VERSION
monitoring      default         1               2025-02-05 16:38:17.9310121 +0600 +06   deployed        kube-prometheus-stack-68.4.5      v0.79.2

Sndevice@Sndevice MINGW64 ~/myownchart
$ kubectl get po
NAME                                                     READY   STATUS    RESTARTS      AGE
alertmanager-monitoring-kube-prometheus-alertmanager-0   2/2     Running   0             3m42s
mongo-deployment-687945d745-ksrvm                        1/1     Running   2 (75m ago)   12d
mongo-deployment-687945d745-wdjr2                        1/1     Running   2 (75m ago)   12d
monitoring-grafana-5476f8cc5c-jhzsz                      3/3     Running   0             4m49s
monitoring-kube-prometheus-operator-7859f9db49-4qbcg     1/1     Running   0             4m49s
monitoring-kube-state-metrics-56d9ddb9c7-dfswp           1/1     Running   0             4m49s
monitoring-prometheus-node-exporter-x8gq2                1/1     Running   0             4m49s
prometheus-monitoring-kube-prometheus-prometheus-0       2/2     Running   0             3m42s
webapp-deployment-5d5f84c76c-s5c2n                       1/1     Running   3 (75m ago)   12d

Sndevice@Sndevice MINGW64 ~/myownchart
$ kubectl get svc
NAME                                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
alertmanager-operated                     ClusterIP   None             <none>        9093/TCP,9094/TCP,9094/UDP   39m
kubernetes                                ClusterIP   10.96.0.1        <none>        443/TCP                      17d
mongo-service                             ClusterIP   10.109.210.174   <none>        27017/TCP                    13d
monitoring-grafana                        ClusterIP   10.100.97.119    <none>        80/TCP                       40m
monitoring-kube-prometheus-alertmanager   ClusterIP   10.100.191.29    <none>        9093/TCP,8080/TCP            40m
monitoring-kube-prometheus-operator       ClusterIP   10.96.13.107     <none>        443/TCP                      40m
monitoring-kube-prometheus-prometheus     ClusterIP   10.100.216.145   <none>        9090/TCP,8080/TCP            40m
monitoring-kube-state-metrics             ClusterIP   10.109.197.2     <none>        8080/TCP                     40m
monitoring-prometheus-node-exporter       ClusterIP   10.96.119.51     <none>        9100/TCP                     40m
prometheus-operated                       ClusterIP   None             <none>        9090/TCP                     39m
webapp-service                            NodePort    10.101.192.143   <none>        3000:30200/TCP               13d

Sndevice@Sndevice MINGW64 ~/myownchart
$ kubectl edit svc ^C

Sndevice@Sndevice MINGW64 ~/myownchart
$ kubectl get svc monitoring-grafana
NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
monitoring-grafana   ClusterIP   10.100.97.119   <none>        80/TCP    44m

Sndevice@Sndevice MINGW64 ~/myownchart
$ kubectl edit svc monitoring-grafana
service/monitoring-grafana edited

Sndevice@Sndevice MINGW64 ~/myownchart
$ kubectl get svc
NAME                                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
alertmanager-operated                     ClusterIP   None             <none>        9093/TCP,9094/TCP,9094/UDP   46m
kubernetes                                ClusterIP   10.96.0.1        <none>        443/TCP                      17d
mongo-service                             ClusterIP   10.109.210.174   <none>        27017/TCP                    13d
monitoring-grafana                        NodePort    10.100.97.119    <none>        80:30001/TCP                 47m
monitoring-kube-prometheus-alertmanager   ClusterIP   10.100.191.29    <none>        9093/TCP,8080/TCP            47m
monitoring-kube-prometheus-operator       ClusterIP   10.96.13.107     <none>        443/TCP                      47m
monitoring-kube-prometheus-prometheus     ClusterIP   10.100.216.145   <none>        9090/TCP,8080/TCP            47m
monitoring-kube-state-metrics             ClusterIP   10.109.197.2     <none>        8080/TCP                     47m
monitoring-prometheus-node-exporter       ClusterIP   10.96.119.51     <none>        9100/TCP                     47m
prometheus-operated                       ClusterIP   None             <none>        9090/TCP                     46m
webapp-service                            NodePort    10.101.192.143   <none>        3000:30200/TCP               13d

Sndevice@Sndevice MINGW64 ~/myownchart
$ minikube ip
192.168.49.2
Sndevice@Sndevice MINGW64 ~/myownchart
$ minikube service monitoring-grafana --url
http://127.0.0.1:63800
❗  Because you are using a Docker driver on windows, the terminal needs to be open to run it.
```

9th feb, 2025

I have completed the helm tutorial but i needed some extra practice to get along with the relevant command lines. So today i have started working on the kube prometheus stack again and pulled it from helm repo and installed and configured it. 

```bash
Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings (main)       
$ helm repo list
NAME                    URL
prometheus-community    https://prometheus-community.github.io/helm-charts  

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings (main)       
$ mkdir may-helm

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings (main)       
$ cd may-helm

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm (main)
$ helm pull prometheus-community/kube-prometheus-stack --untar=true

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm (main)
$ ls
kube-prometheus-stack/

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm (main)
$ cd ..

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings (main)       
$ cd .

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings (main)       
$ cd ./

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings (main)       
$ cd may-helm

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm (main)
$ ls
kube-prometheus-stack/

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm (main)
$ cd kube-prometheus-stack/

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm/kube-prometheus-stack (main)
$ vscode .
Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm/kube-prometheus-stack (main)
Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm/kube-prometheus-stack (main)
$ vscode .
bash: vscode: command not found
Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm/kube-prometheus-stack (main)
$ vscode .
Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm/kube-prometheus-stack (main)
Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm/kube-prometheus-stack (main)
Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm/kube-prometheus-stack (main)
$ vscode .
bash: vscode: command not found

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm/kube-prometheus-stack (main)
$ cd ..

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm (main)
$ helm install monitoring ./kube-prometheus-stack/
NAME: monitoring
LAST DEPLOYED: Sun Feb  9 14:30:03 2025
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
4 -d ; echo

Access Grafana local instance:

  export POD_NAME=$(kubectl --namespace default get pod -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=monitoring" -oname)
  kubectl --namespace default port-forward $POD_NAME 3000

Visit https://github.com/prometheus-operator/kube-prometheus for instructions on how to create & configure Alertmanager and Prometheus instances using the Operator.

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm (main)
$ ls
kube-prometheus-stack/

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm (main)
$ cd kube-prometheus-stack/

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm/kube-prometheus-stack (main)
$ helm upgrade monitoring --value=myvalues.yaml .
Error: unknown flag: --value

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm/kube-prometheus-stack (main)
$ helm upgrade monitoring --values=myvalues.yaml .
Release "monitoring" has been upgraded. Happy Helming!
NAME: monitoring
LAST DEPLOYED: Sun Feb  9 15:11:23 2025
NAMESPACE: default
STATUS: deployed
REVISION: 2
NOTES:
kube-prometheus-stack has been installed. Check its status by running:
  kubectl --namespace default get pods -l "release=monitoring"

Get Grafana 'admin' user password by running:

  kubectl --namespace default get secrets prom-grafana -ojsonpath="{.data.admin-password}" | base64 -d ; echo

Access Grafana local instance:

  export POD_NAME=$(kubectl --namespace default get pod -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=monitoring" -oname)
  kubectl --namespace default port-forward $POD_NAME 3000

Visit https://github.com/prometheus-operator/kube-prometheus for instructions on how to create & configure Alertmanager and Prometheus instances using the Operator.

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm/kube-prometheus-stack (main)
$ kubectl get svc
NAME                                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
alertmanager-operated                     ClusterIP   None             <none>        9093/TCP,9094/TCP,9094/UDP   41m
kubernetes                                ClusterIP   10.96.0.1        <none>        443/TCP                      21d
mongo-service                             ClusterIP   10.109.210.174   <none>        27017/TCP                    17d
monitoring-grafana                        NodePort    10.103.141.134   <none>        80:30001/TCP                 42m
monitoring-kube-prometheus-alertmanager   ClusterIP   10.100.95.138    <none>        9093/TCP,8080/TCP            42m
monitoring-kube-prometheus-operator       ClusterIP   10.107.61.171    <none>        443/TCP                      42m
monitoring-kube-prometheus-prometheus     ClusterIP   10.101.151.1     <none>        9090/TCP,8080/TCP            42m
monitoring-kube-state-metrics             ClusterIP   10.107.165.110   <none>        8080/TCP                     42m
monitoring-prometheus-node-exporter       ClusterIP   10.102.67.220    <none>        9100/TCP                     42m
prometheus-operated                       ClusterIP   None             <none>        9090/TCP                     41m
webapp-service                            NodePort    10.101.192.143   <none>        3000:30200/TCP               17d

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm/kube-prometheus-stack (main)
$ kubectl get po
NAME                                                     READY   STATUS    RESTARTS   AGE
alertmanager-monitoring-kube-prometheus-alertmanager-0   2/2     Running   0          42m
monitoring-grafana-565b44c7f-8bzvl                       3/3     Running   0          87s
monitoring-kube-prometheus-operator-7859f9db49-5xnd6     1/1     Running   0          42m
monitoring-kube-state-metrics-56d9ddb9c7-2jv8f           1/1     Running   0          42m
monitoring-prometheus-node-exporter-qsdlj                1/1     Running   0          42m
prometheus-monitoring-kube-prometheus-prometheus-0       2/2     Running   0          42m
```

Passed the the myvalues.yaml file as a values parameter externally.

```yaml
grafana:
  adminPassword: admin
  service:
    type: NodePort
    nodePort: 30001
```

In the following bash, i have created a k8s yaml file from a helm chart. 

```bash
Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm/kube-prometheus-stack (main)
$ helm template monitoring . --values=myvalues.yaml > monitoring-stack.yaml

Sndevice@Sndevice MINGW64 ~/Documents/GitHub/myalice-learnings/may-helm/kube-prometheus-stack (main)
$ ls
Chart.lock  Chart.yaml  charts/  monitoring-stack.yaml  myvalues.yaml  templates/  values.yaml
```
Now it becomes a standalone monitoring stack yaml file which can be deployed by kubectl apply command without the actual helm chart.

I have tried the fleetman app from Richard Chesterwood and deployed it in a cluster.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
spec:
  selector:
    matchLabels:
      app: webapp
  replicas: 1
  template: # template for the pods
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: webapp
        # Note to deployer - add -dev at the end of here for development version
        image: richardchesterwood/k8s-fleetman-helm-demo:v1.0.0
---
apiVersion: v1
kind: Service
metadata:
  name: fleetman-webapp

spec:
  selector:
    app: webapp

  ports:
    - name: http
      port: 80
      nodePort: 30080

  type: NodePort
```

This works with a service tunnel and shows the content on the browser.

```bash
minikube service fleetman-webapp --url
```

we can make our yaml file dynamic by passing values using go lang syntax. In the following example, i have used go lang syntax `.Values.webapp.numofreplicas` in curly brackets to purse the value from values.yaml file in the same directory.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
spec:
  selector:
    matchLabels:
      app: webapp
  replicas: {{.Values.webapp.numofreplicas}}
  template: # template for the pods
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: webapp
        # Note to deployer - add -dev at the end of here for development version
        image: richardchesterwood/k8s-fleetman-helm-demo:v1.0.0
---
apiVersion: v1
kind: Service
metadata:
  name: fleetman-webapp

spec:
  selector:
    app: webapp

  ports:
    - name: http
      port: 80
      nodePort: 30080

  type: NodePort
```

where the values.yaml file looks like this.

```yaml
webapp:
  numofreplicas: 3
```

Using helm template, we can view the changes without deploying it.


#### Prometheus

Metric Entries: help and type

Help: The description about the specific metric.

Type: there are 3 types of metrics
1. Counter: How many times x happened?
2. Gauge: What is the current value of x now?
3. Histogram: How long or how big the x was?

Here the value of x is for example cpu usage or number of requests etc.

Exposing the prometheus-server Kubernetes Service:

`kubectl expose service prometheus-server --type=NodePort --target-port=9090 --name=prometheus-server-ext`

Now we have exposed the prometheus-server Kubernetes service. Let’s access the Prometheus application using the following command:

`minikube service prometheus-server-ext`

Check the additional scrape config for the kube prometheus stack and insert additional target to test.


![image][def]

[def]: files://C:/Users/Sndevice/Documents/GitHub/myalice-learnings/may-helm/kube-prometheus-stack/pvc.png


![pvcenable](C:\Users\Sndevice\Documents\GitHub\myalice-learnings\may-helm\kube-prometheus-stack\pvc.png)




feb 24, 2025

Today i have created a namespace named nginxspace and deployed an nginx with service under that namespace. 

this is the deployment file.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  replicas: 1
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
        image: nginx:latest
        ports:
        - containerPort: 80
```

and this is the service file.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```
following codes are the bash lines.

```bash
snehashis@sndevice:~/nginx$ kubectl apply -f Deployment.yaml 
deployment.apps/nginx created
snehashis@sndevice:~/nginx$ kubectl apply -f Service.yaml
service/nginx-service created
snehashis@sndevice:~/nginx$ minikube service nginx-service
|-----------|---------------|-------------|---------------------------|
| NAMESPACE |     NAME      | TARGET PORT |            URL            |
|-----------|---------------|-------------|---------------------------|
| default   | nginx-service |          80 | http://192.168.49.2:32726 |
|-----------|---------------|-------------|---------------------------|

❌  Exiting due to SVC_UNREACHABLE: service not available: no running pod for service nginx-service found

╭───────────────────────────────────────────────────────────────────────────────────────────╮
│                                                                                           │
│    😿  If the above advice does not help, please let us know:                             │
│    👉  https://github.com/kubernetes/minikube/issues/new/choose                           │
│                                                                                           │
│    Please run `minikube logs --file=logs.txt` and attach logs.txt to the GitHub issue.    │
│    Please also attach the following file to the GitHub issue:                             │
│    - /tmp/minikube_service_b4fe9e6a1b6223beaf0f602992e1a84ac8b89ecc_0.log                 │
│                                                                                           │
╰───────────────────────────────────────────────────────────────────────────────────────────╯

snehashis@sndevice:~/nginx$ kubectl get svc
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes      ClusterIP   10.96.0.1       <none>        443/TCP        34m
nginx-service   NodePort    10.97.210.141   <none>        80:32726/TCP   2m6s
snehashis@sndevice:~/nginx$ kubectl get po
NAME                   READY   STATUS    RESTARTS   AGE
nginx-96b9d695-vgjjb   1/1     Running   0          2m46s
snehashis@sndevice:~/nginx$ kubectl get po -A
NAMESPACE              NAME                                         READY   STATUS    RESTARTS        AGE
default                nginx-96b9d695-vgjjb                         1/1     Running   0               2m55s
kube-system            coredns-668d6bf9bc-9bnxg                     1/1     Running   1 (10m ago)     35m
kube-system            etcd-minikube                                1/1     Running   1 (10m ago)     35m
kube-system            kube-apiserver-minikube                      1/1     Running   1 (10m ago)     35m
kube-system            kube-controller-manager-minikube             1/1     Running   1 (10m ago)     35m
kube-system            kube-proxy-28z8w                             1/1     Running   1 (10m ago)     35m
kube-system            kube-scheduler-minikube                      1/1     Running   1 (10m ago)     35m
kube-system            storage-provisioner                          1/1     Running   3 (3m50s ago)   35m
kubernetes-dashboard   dashboard-metrics-scraper-5d59dccf9b-d9xns   1/1     Running   1 (10m ago)     28m
kubernetes-dashboard   kubernetes-dashboard-7779f9b69b-dhnwr        1/1     Running   2 (3m50s ago)   28m
snehashis@sndevice:~/nginx$ kubectl get sts
No resources found in default namespace.
snehashis@sndevice:~/nginx$ kubectl get ds
No resources found in default namespace.
snehashis@sndevice:~/nginx$ kubectl get deploy
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           4m5s
snehashis@sndevice:~/nginx$ kubectl delete deploy nginx
deployment.apps "nginx" deleted
snehashis@sndevice:~/nginx$ kubectl get deploy
No resources found in default namespace.
snehashis@sndevice:~/nginx$ kubectl get ds
No resources found in default namespace.
snehashis@sndevice:~/nginx$ kubectl get po -A
NAMESPACE              NAME                                         READY   STATUS    RESTARTS        AGE
kube-system            coredns-668d6bf9bc-9bnxg                     1/1     Running   1 (12m ago)     37m
kube-system            etcd-minikube                                1/1     Running   1 (12m ago)     37m
kube-system            kube-apiserver-minikube                      1/1     Running   1 (12m ago)     37m
kube-system            kube-controller-manager-minikube             1/1     Running   1 (12m ago)     37m
kube-system            kube-proxy-28z8w                             1/1     Running   1 (12m ago)     37m
kube-system            kube-scheduler-minikube                      1/1     Running   1 (12m ago)     37m
kube-system            storage-provisioner                          1/1     Running   3 (5m47s ago)   37m
kubernetes-dashboard   dashboard-metrics-scraper-5d59dccf9b-d9xns   1/1     Running   1 (12m ago)     30m
kubernetes-dashboard   kubernetes-dashboard-7779f9b69b-dhnwr        1/1     Running   2 (5m47s ago)   30m
snehashis@sndevice:~/nginx$ kubectl apply -f Deployment.yaml -n nginxspace
deployment.apps/nginx created
snehashis@sndevice:~/nginx$ kubectl get svc -A
NAMESPACE              NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                  AGE
default                kubernetes                  ClusterIP   10.96.0.1       <none>        443/TCP                  38m
default                nginx-service               NodePort    10.97.210.141   <none>        80:32726/TCP             5m33s
kube-system            kube-dns                    ClusterIP   10.96.0.10      <none>        53/UDP,53/TCP,9153/TCP   38m
kubernetes-dashboard   dashboard-metrics-scraper   ClusterIP   10.101.97.50    <none>        8000/TCP                 31m
kubernetes-dashboard   kubernetes-dashboard        ClusterIP   10.111.48.237   <none>        80/TCP                   31m
snehashis@sndevice:~/nginx$ kubectl delete svc nginx-service
service "nginx-service" deleted
snehashis@sndevice:~/nginx$ kubectl apply -f Service.yaml -n nginxspace
service/nginx-service created
snehashis@sndevice:~/nginx$ kubectl get svc -A
NAMESPACE              NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                  AGE
default                kubernetes                  ClusterIP   10.96.0.1       <none>        443/TCP                  38m
kube-system            kube-dns                    ClusterIP   10.96.0.10      <none>        53/UDP,53/TCP,9153/TCP   38m
kubernetes-dashboard   dashboard-metrics-scraper   ClusterIP   10.101.97.50    <none>        8000/TCP                 32m
kubernetes-dashboard   kubernetes-dashboard        ClusterIP   10.111.48.237   <none>        80/TCP                   32m
nginxspace             nginx-service               NodePort    10.97.34.185    <none>        80:31619/TCP             5s
snehashis@sndevice:~/nginx$ kubectl get po -A
NAMESPACE              NAME                                         READY   STATUS    RESTARTS        AGE
kube-system            coredns-668d6bf9bc-9bnxg                     1/1     Running   1 (14m ago)     38m
kube-system            etcd-minikube                                1/1     Running   1 (14m ago)     38m
kube-system            kube-apiserver-minikube                      1/1     Running   1 (14m ago)     38m
kube-system            kube-controller-manager-minikube             1/1     Running   1 (14m ago)     38m
kube-system            kube-proxy-28z8w                             1/1     Running   1 (14m ago)     38m
kube-system            kube-scheduler-minikube                      1/1     Running   1 (14m ago)     38m
kube-system            storage-provisioner                          1/1     Running   3 (7m30s ago)   38m
kubernetes-dashboard   dashboard-metrics-scraper-5d59dccf9b-d9xns   1/1     Running   1 (14m ago)     32m
kubernetes-dashboard   kubernetes-dashboard-7779f9b69b-dhnwr        1/1     Running   2 (7m30s ago)   32m
nginxspace             nginx-96b9d695-5zfl9                         1/1     Running   0               75s
snehashis@sndevice:~/nginx$ kubectl get deploy -A
NAMESPACE              NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
kube-system            coredns                     1/1     1            1           39m
kubernetes-dashboard   dashboard-metrics-scraper   1/1     1            1           32m
kubernetes-dashboard   kubernetes-dashboard        1/1     1            1           32m
nginxspace             nginx                       1/1     1            1           86s
snehashis@sndevice:~/nginx$ minikube service nginx-service

❌  Exiting due to SVC_NOT_FOUND: Service 'nginx-service' was not found in 'default' namespace.
You may select another namespace by using 'minikube service nginx-service -n <namespace>'. Or list out all the services using 'minikube service list'

snehashis@sndevice:~/nginx$ minikube service nginx-service -n nginxspace
|------------|---------------|-------------|---------------------------|
| NAMESPACE  |     NAME      | TARGET PORT |            URL            |
|------------|---------------|-------------|---------------------------|
| nginxspace | nginx-service |          80 | http://192.168.49.2:31619 |
|------------|---------------|-------------|---------------------------|
🎉  Opening service nginxspace/nginx-service in default browser...
```

After that i have created another deployment and created a seperate namespace yaml file and and pushed it to git repo.

Following codes are for deployment file

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  namespace: nginxspace
  labels:
    app: nginx
spec:
  replicas: 1
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
        image: nginx:latest
        ports:
        - containerPort: 80
```

following codes are for namespace file.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: nginxspace2
```
then we applied these in bash.

```bash
snehashis@sndevice:~/nginx2$ kubectl create -f ./Namespace.yaml 
error: no objects passed to create
snehashis@sndevice:~/nginx2$ kubectl create -f Namespace.yaml 
error: no objects passed to create
snehashis@sndevice:~/nginx2$ ls
Deployment.yaml  Namespace.yaml
snehashis@sndevice:~/nginx2$ kubectl create -f ./Namespace.yaml 
error: no objects passed to create
snehashis@sndevice:~/nginx2$ ls
Deployment.yaml  Namespace.yaml
snehashis@sndevice:~/nginx2$ kubectl create -f ./Namespace.yaml 
namespace/nginxspace2 created
snehashis@sndevice:~/nginx2$ git config --global user.email "rupam.sdnath@gmail.com"
snehashis@sndevice:~/nginx2$ git config --global user.name "Snehashis-sci"
snehashis@sndevice:~/nginx2$ git status
On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   Deployment.yaml
        new file:   Namespace.yaml

snehashis@sndevice:~/nginx2$ git commit
[main (root-commit) bc489b2] created an nginx deployment and namespace yaml file for nginx2
 2 files changed, 25 insertions(+)
 create mode 100644 Deployment.yaml
 create mode 100644 Namespace.yaml
snehashis@sndevice:~/nginx2$ git status
On branch main
nothing to commit, working tree clean
snehashis@sndevice:~/nginx2$ git push
fatal: No configured push destination.
Either specify the URL from the command-line or configure a remote repository using

    git remote add <name> <url>

and then push using the remote name

    git push <name>

snehashis@sndevice:~/nginx2$ git remote add origin https://github.com/Snehashis-sci/nginx2.git
snehashis@sndevice:~/nginx2$ git branch -M main
snehashis@sndevice:~/nginx2$ git push -u origin main
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 8 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 517 bytes | 172.00 KiB/s, done.
Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/Snehashis-sci/nginx2.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
snehashis@sndevice:~/nginx2$ git status
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
snehashis@sndevice:~/nginx2$ kubectl get ns -A
NAME                   STATUS   AGE
default                Active   157m
flux-system            Active   2m15s
kube-node-lease        Active   157m
kube-public            Active   157m
kube-system            Active   157m
kubernetes-dashboard   Active   151m
nginxspace             Active   145m
nginxspace2            Active   101m
snehashis@sndevice:~/nginx2$ git push
To https://github.com/Snehashis-sci/nginx2.git
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'https://github.com/Snehashis-sci/nginx2.git'
hint: Updates were rejected because the remote contains work that you do not
hint: have locally. This is usually caused by another repository pushing to
hint: the same ref. If you want to integrate the remote changes, use
hint: 'git pull' before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
snehashis@sndevice:~/nginx2$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   Deployment.yaml

no changes added to commit (use "git add" and/or "git commit -a")
snehashis@sndevice:~/nginx2$ git add .
snehashis@sndevice:~/nginx2$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   Deployment.yaml

snehashis@sndevice:~/nginx2$ git commit
[main 44ea032] added namespace
 1 file changed, 1 insertion(+)
snehashis@sndevice:~/nginx2$ git push
To https://github.com/Snehashis-sci/nginx2.git
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'https://github.com/Snehashis-sci/nginx2.git'
hint: Updates were rejected because the remote contains work that you do not
hint: have locally. This is usually caused by another repository pushing to
hint: the same ref. If you want to integrate the remote changes, use
hint: 'git pull' before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
snehashis@sndevice:~/nginx2$ git push origin main
To https://github.com/Snehashis-sci/nginx2.git
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'https://github.com/Snehashis-sci/nginx2.git'
hint: Updates were rejected because the remote contains work that you do not
hint: have locally. This is usually caused by another repository pushing to
hint: the same ref. If you want to integrate the remote changes, use
hint: 'git pull' before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
snehashis@sndevice:~/nginx2$ git pull origin main --rebase
remote: Enumerating objects: 10, done.
remote: Counting objects: 100% (10/10), done.
remote: Compressing objects: 100% (8/8), done.
remote: Total 9 (delta 1), reused 8 (delta 0), pack-reused 0 (from 0)
Unpacking objects: 100% (9/9), 58.78 KiB | 445.00 KiB/s, done.
From https://github.com/Snehashis-sci/nginx2
 * branch            main       -> FETCH_HEAD
   bc489b2..dde59d2  main       -> origin/main
Successfully rebased and updated refs/heads/main.
snehashis@sndevice:~/nginx2$ git push origin main
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 384 bytes | 384.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/Snehashis-sci/nginx2.git
   dde59d2..17f2347  main -> main
snehashis@sndevice:~/nginx2$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   flux-system/gotk-sync.yaml

no changes added to commit (use "git add" and/or "git commit -a")
snehashis@sndevice:~/nginx2$ git add .
snehashis@sndevice:~/nginx2$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   flux-system/gotk-sync.yaml

snehashis@sndevice:~/nginx2$ git commit
[main db23de0] changed the kustomization spec interval from 10mins to 2mins
 1 file changed, 1 insertion(+), 1 deletion(-)
snehashis@sndevice:~/nginx2$ git push
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 8 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 449 bytes | 449.00 KiB/s, done.
Total 4 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To https://github.com/Snehashis-sci/nginx2.git
   17f2347..db23de0  main -> main
snehashis@sndevice:~/nginx2$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   Deployment.yaml

no changes added to commit (use "git add" and/or "git commit -a")
snehashis@sndevice:~/nginx2$ git add .
snehashis@sndevice:~/nginx2$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   Deployment.yaml

snehashis@sndevice:~/nginx2$ git commit
Aborting commit due to empty commit message.
snehashis@sndevice:~/nginx2$ git commit
[main a582cbc] commented out the deployment
 1 file changed, 22 insertions(+), 22 deletions(-)
snehashis@sndevice:~/nginx2$ git push
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 512 bytes | 512.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/Snehashis-sci/nginx2.git
   db23de0..a582cbc  main -> main
snehashis@sndevice:~/nginx2$ git add .
snehashis@sndevice:~/nginx2$ git commit -m "hudai"
[main 0c9337e] hudai
 1 file changed, 22 insertions(+), 22 deletions(-)
snehashis@sndevice:~/nginx2$ git push origin main
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 490 bytes | 245.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/Snehashis-sci/nginx2.git
   a582cbc..0c9337e  main -> main
snehashis@sndevice:~/nginx2$ git status
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

Then i have install flux cli and then installed flux using Flux bootstrap for GitHub reference. Connected my git repo to flux.


the following bash lines were for yq configuration where the yq tool wasnot showing the yaml file in processed template. So i had to tweak it a little bit to get working.

```bash
snehashis@sndevice:~$ kubectl -n nginxspace get deploy nginx -oyaml | yq -y .metadata
annotations:
  deployment.kubernetes.io/revision: '1'
creationTimestamp: '2025-02-24T08:35:28Z'
generation: 2
labels:
  app: nginx
  kustomize.toolkit.fluxcd.io/name: flux-system
  kustomize.toolkit.fluxcd.io/namespace: flux-system
name: nginx
namespace: nginxspace
resourceVersion: '12427'
uid: 6de95b63-20e5-4ced-b3da-304292f85bc0
snehashis@sndevice:~$ yq --version
yq 0.0.0
snehashis@sndevice:~$ sudo apt info yq
Package: yq
Version: 3.1.0-3
Priority: optional
Section: universe/utils
Origin: Ubuntu
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Original-Maintainer: Debian Python Team <team+python@tracker.debian.org>
Bugs: https://bugs.launchpad.net/ubuntu/+filebug
Installed-Size: 70.7 kB
Depends: jq, python3-argcomplete, python3-toml, python3-xmltodict, python3-yaml, python3:any
Homepage: https://github.com/kislyuk/yq
Download-Size: 16.9 kB
APT-Manual-Installed: yes
APT-Sources: https://mirror.limda.net/ubuntu-archive noble/universe amd64 Packages
Description: Command-line YAML processor - jq wrapper for YAML documents
 yq transcodes YAML documents to JSON and passes them to jq. The
 package also includes xq, which transcodes XML to JSON using
 xmltodict and pipes it to jq, and tomlq, which uses the toml library
 to transcode TOML to JSON, then pipes it to jq

snehashis@sndevice:~$ sudo snap info yq
name:      yq
summary:   A lightweight and portable command-line YAML processor
publisher: Mike Farah (mikefarah)
store-url: https://snapcraft.io/yq
contact:   https://github.com/mikefarah/yq/issues
license:   unset
description: |
  The aim of the project is to be the jq or sed of yaml files.
commands:
  - yq
snap-id:      b1xa1ED1Aw4HN9BnJVP3Je95pyEVN6gu
tracking:     latest/stable
refresh-date: today at 17:24 +06
channels:
  latest/stable:    v4.44.5 2024-12-07 (2634) 7MB -
  latest/candidate: ↑                             
  latest/beta:      ↑                             
  latest/edge:      v4.45.1 2025-02-22 (2661) 7MB -
  v4/stable:        v4.44.5 2024-12-07 (2634) 7MB -
  v4/candidate:     ↑                             
  v4/beta:          ↑                             
  v4/edge:          ↑                             
  v3/stable:        3.4.1   2021-01-05  (884) 2MB -
  v3/candidate:     ↑                             
  v3/beta:          ↑                             
  v3/edge:          3.4.1   2021-01-05  (884) 2MB -
installed:          v4.44.5            (2634) 7MB -
snehashis@sndevice:~$ whereis yq
yq: /usr/bin/yq /snap/bin/yq
snehashis@sndevice:~$ sudo apt-get remove yq
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages were automatically installed and are no longer required:
  python3-argcomplete python3-toml python3-xmltodict
Use 'sudo apt autoremove' to remove them.
The following packages will be REMOVED:
  yq
0 upgraded, 0 newly installed, 1 to remove and 1 not upgraded.
After this operation, 70.7 kB disk space will be freed.
Do you want to continue? [Y/n] y
(Reading database ... 185110 files and directories currently installed.)
Removing yq (3.1.0-3) ...
snehashis@sndevice:~$ yq --version
bash: /usr/bin/yq: No such file or directory
snehashis@sndevice:~$ whereis yq
yq: /snap/bin/yq
snehashis@sndevice:~$ yq --version
bash: /usr/bin/yq: No such file or directory
snehashis@sndevice:~$ ls -lthr /snap/bin/yq
lrwxrwxrwx 1 root root 13 Feb 24 17:24 /snap/bin/yq -> /usr/bin/snap
snehashis@sndevice:~$ ls -lthr /usr/bin/snap
-rwxr-xr-x 1 root root 18M Oct 11 14:05 /usr/bin/snap
snehashis@sndevice:~$ sudo ln -sf /usr/bin/snap /usr/bin/yq
snehashis@sndevice:~$ yq --version
yq (https://github.com/mikefarah/yq/) version v4.44.5
snehashis@sndevice:~$ kubectl -n nginxspace get deploy nginx -oyaml | yq  .metadata
annotations:
  deployment.kubernetes.io/revision: "1"
creationTimestamp: "2025-02-24T08:35:28Z"
generation: 2
labels:
  app: nginx
  kustomize.toolkit.fluxcd.io/name: flux-system
  kustomize.toolkit.fluxcd.io/namespace: flux-system
name: nginx
namespace: nginxspace
resourceVersion: "12427"
uid: 6de95b63-20e5-4ced-b3da-304292f85bc0
snehashis@sndevice:~$ whereis yq
yq: /usr/bin/yq /snap/bin/yq
snehashis@sndevice:~$ ls -lthr /usr/bin/yq
lrwxrwxrwx 1 root root 13 Feb 24 17:57 /usr/bin/yq -> /usr/bin/snap
snehashis@sndevice:~$ ls -lthr /snap/bin/yq
lrwxrwxrwx 1 root root 13 Feb 24 17:24 /snap/bin/yq -> /usr/bin/snap
```

So today i have learned that flux doesnt hamper any existing pod or deployment under a namespace. And flux uses automation to keep the cluster up and running even if we comment out a deployment. Later i have to check the git deployment and change it's namespace to check if it changes anything. 



March 03, 2025

#### Pulumi and Kubernetes

pulumi Iac (Infrastruce as code)

Its simply an approach to automate provisioning and management of infrastructure. its important for the ongoing transition to cloud environments. Since frequent infrastructure changes are happening, hundred of team members can use the cloud environment to take the advantages of the possible made changes swiftly. Thounsands of changes of resources can be painful to keep track. So, automation of such system can benefit the team members to take control of such complexity. 
Using Pulumi to deploy and manage Kubernetes applications and learn best practices for creating maintainable and scalable infrastructure code. By using Pulumi, organizations can streamline their infrastructure management, improve collaboration between development and operations teams, and accelerate their cloud-native journey.


#### fluxcd

Monorepo: Stores all k8s files in a single git repo. Stores all environment specific configs in the same branch.

Repo structure:

├── apps
│   ├── base
│   ├── production 
│   └── staging
├── infrastructure
│   ├── base
│   ├── production 
│   └── staging
└── clusters
    ├── production
    └── staging


Repo per env: It is similar to monorepo but there are some notalble differences. 
1. In monorepo, all the team members can read the production config since git is not designed to restrict access. It can be done by tools like github or gitlab. Through these tools, we can provide permission maangement on top of git. having a seperate repo for production means we can grant acccess to a subset of team members while everyone can clone staging and open pull requests. 
2. Promoting changes from one env to another can be time consuming for example infrastructure changes which can't be automated with flux image updates.
3. Using the same repo for all env, inintentional changes to production can be harder to spot. having a dedicated production repo can limit the scope of changes and makes the review process less error prone.

Repo per team: the org has a dedicated admin team that provides k8s as a service to other teams. 

Admin team is responsible for 
* setting up the staging and prod env.
* maintain the cluster addon and othe cluster wide resources like CRDs, controllers, admission webhooks.
* onboards the dev teams repo using flux's gitrepo custom resources.
* configures how the dev teams repo are reconciled on each cluster using flux's kustomization custom resources.

The dev teams are responsible for
* setting uo the apps definitions such as k8s deployments, helm release.
* configures how the apps are reconciled on each env(kustomize overlays, helm values)
* manages the apps promotion between envs using flux's automated image updates to git.

A good example can be found here: https://github.com/fluxcd/flux2-multi-tenancy

Repo per app: In this way, the same repo store both the app source code and its deployment manifests. The deployment manifests from an app repo can serve as the base config for both the monorepo and the repo per team approaches.
Instead of duplicating the deployment manifests between the app repo and the cluster config repo, the config repo can hold a pointer to the app manifests.

Inside the config repo, we can define a gitrepo that tells flux to clone the app repo inside the cluster, then with a kustomization, we can tell flux which directory holds the app manifests and how to patch them based on the target env.

Another option is to bundle the app manifests into a helm chart and publish it to helm repo. In the config repo, we can define the helm repo and create an app helmrelease for each target env.



```bashrc
snehashis@sndevice:~$ sops -version
sops 3.9.4 (latest)
snehashis@sndevice:~$ wget -0 age.tar.gz https://github.com/FiloSottile/age/releases/download/v1.2.1/age-v1.2.1-linux-amd64.tar.gz
wget: invalid option -- '0'
Usage: wget [OPTION]... [URL]...

Try `wget --help' for more options.
snehashis@sndevice:~$ wget -O age.tar.gz https://github.com/FiloSottile/age/releases/download/v1.2.1/age-v1.2.1-linux-amd64.tar.gz
--2025-03-05 14:17:23--  https://github.com/FiloSottile/age/releases/download/v1.2.1/age-v1.2.1-linux-amd64.tar.gz
Resolving github.com (github.com)... 20.205.243.166
Connecting to github.com (github.com)|20.205.243.166|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://objects.githubusercontent.com/github-production-release-asset-2e65be/187403699/fbe9109d-73fc-472f-be1e-096da3f42be7?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=releaseassetproduction%2F20250305%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20250305T081723Z&X-Amz-Expires=300&X-Amz-Signature=70ed687ac30b3302d40a7eeca6e9a480cc4c071f2efa1f3b8d80417da8133aec&X-Amz-SignedHeaders=host&response-content-disposition=attachment%3B%20filename%3Dage-v1.2.1-linux-amd64.tar.gz&response-content-type=application%2Foctet-stream [following]
--2025-03-05 14:17:23--  https://objects.githubusercontent.com/github-production-release-asset-2e65be/187403699/fbe9109d-73fc-472f-be1e-096da3f42be7?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=releaseassetproduction%2F20250305%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20250305T081723Z&X-Amz-Expires=300&X-Amz-Signature=70ed687ac30b3302d40a7eeca6e9a480cc4c071f2efa1f3b8d80417da8133aec&X-Amz-SignedHeaders=host&response-content-disposition=attachment%3B%20filename%3Dage-v1.2.1-linux-amd64.tar.gz&response-content-type=application%2Foctet-stream
Resolving objects.githubusercontent.com (objects.githubusercontent.com)... 185.199.110.133, 185.199.111.133, 185.199.109.133, ...
Connecting to objects.githubusercontent.com (objects.githubusercontent.com)|185.199.110.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 5194720 (5.0M) [application/octet-stream]
Saving to: ‘age.tar.gz’

age.tar.gz                                      100%[=====================================================================================================>]   4.95M  2.46MB/s    in 2.0s    

2025-03-05 14:17:27 (2.46 MB/s) - ‘age.tar.gz’ saved [5194720/5194720]

snehashis@sndevice:~$ ls
age.tar.gz  Desktop  Documents  Downloads  Music  myalice  nginx  nginx2  Pictures  prometheus  Public  pulumidemo  snap  Templates  Videos
snehashis@sndevice:~$ tar xf age.tar.gz 
snehashis@sndevice:~$ ls
age  age.tar.gz  Desktop  Documents  Downloads  Music  myalice  nginx  nginx2  Pictures  prometheus  Public  pulumidemo  snap  Templates  Videos
snehashis@sndevice:~$ cd age
snehashis@sndevice:~/age$ ls
age  age-keygen  LICENSE
snehashis@sndevice:~/age$ cd ..
snehashis@sndevice:~$ sudo mv age/age /usr/local/bin
[sudo] password for snehashis: 
snehashis@sndevice:~$ sudo mv age/age-keygen /usr/local/bin
snehashis@sndevice:~$ ls
age  age.tar.gz  Desktop  Documents  Downloads  Music  myalice  nginx  nginx2  Pictures  prometheus  Public  pulumidemo  snap  Templates  Videos
snehashis@sndevice:~$ cd age
snehashis@sndevice:~/age$ cd ..
snehashis@sndevice:~$ rm -rf age
snehashis@sndevice:~$ rm age.tar.gz 
snehashis@sndevice:~$ age -version
v1.2.1
snehashis@sndevice:~$ age-keygen -version
v1.2.1
snehashis@sndevice:~$ ls
Desktop  Documents  Downloads  Music  myalice  nginx  nginx2  Pictures  prometheus  Public  pulumidemo  snap  Templates  Videos
snehashis@sndevice:~$ cd Documents/
snehashis@sndevice:~/Documents$ ls
task.txt
snehashis@sndevice:~/Documents$ age-keygen -o key.txt
Public key: age10fq6202yhpg3nh0ce32dhz8w64g2xa8lkwux0uzuafnz3ywguuvqqyf68r
snehashis@sndevice:~/Documents$ cat key.txt
# created: 2025-03-05T14:24:12+06:00
# public key: age10fq6202yhpg3nh0ce32dhz8w64g2xa8lkwux0uzuafnz3ywguuvqqyf68r
AGE-SECRET-KEY-18DSZNQSEVHZZJJLAFTXU3NDH54CJWRG8R6CEFVT00HHFUZWTW9SQ2MC3NX
snehashis@sndevice:~/Documents$ cd ~
snehashis@sndevice:~$ whereami
Command 'whereami' not found, but can be installed with:
sudo apt install whereami
snehashis@sndevice:~$ whoami
snehashis
snehashis@sndevice:~$ cd Documents/
snehashis@sndevice:~/Documents$ mkdir ~/.sops
snehashis@sndevice:~/Documents$ mv ./key.txt ~/.sops
snehashis@sndevice:~/Documents$ cd ~
snehashis@sndevice:~$ ls
Desktop  Documents  Downloads  Music  myalice  nginx  nginx2  Pictures  prometheus  Public  pulumidemo  snap  Templates  Videos
snehashis@sndevice:~$ cd ~/.sops
snehashis@sndevice:~/.sops$ ls
key.txt
snehashis@sndevice:~/.sops$ cd ~/
snehashis@sndevice:~$ ls
Desktop  Documents  Downloads  Music  myalice  nginx  nginx2  Pictures  prometheus  Public  pulumidemo  snap  Templates  Videos
snehashis@sndevice:~$ pwd
/home/snehashis
snehashis@sndevice:~$ cd ~/.sops
snehashis@sndevice:~/.sops$ pwd
/home/snehashis/.sops
snehashis@sndevice:~/.sops$ cd ..
snehashis@sndevice:~$ pwd
/home/snehashis
snehashis@sndevice:~$ ls
Desktop  Documents  Downloads  Music  myalice  nginx  nginx2  Pictures  prometheus  Public  pulumidemo  snap  Templates  Videos
snehashis@sndevice:~$ ls ~
Desktop  Documents  Downloads  Music  myalice  nginx  nginx2  Pictures  prometheus  Public  pulumidemo  snap  Templates  Videos
snehashis@sndevice:~$ cat ~/.sops/key.txt 
# created: 2025-03-05T14:24:12+06:00
# public key: age10fq6202yhpg3nh0ce32dhz8w64g2xa8lkwux0uzuafnz3ywguuvqqyf68r
AGE-SECRET-KEY-18DSZNQSEVHZZJJLAFTXU3NDH54CJWRG8R6CEFVT00HHFUZWTW9SQ2MC3NX
```

```bash
snehashis@sndevice:~$ cd Documents/
snehashis@sndevice:~/Documents$ ls
task.txt
snehashis@sndevice:~/Documents$ cd ..
snehashis@sndevice:~$ nano ~/.bashrc
snehashis@sndevice:~$ source ~/.bashrc
snehashis@sndevice:~$ cd Documents/
snehashis@sndevice:~/Documents$ nano secret.yaml
snehashis@sndevice:~/Documents$ cat secret.yaml
apiVersion: v1
kind: Secret
metadata:
    name: mysql-secret
    namespace: default
stringData:
    MYSQL_USER: root
    MYSQL_PASSWORD: ghorerkhobor
snehashis@sndevice:~/Documents$ nano secret.yaml
snehashis@sndevice:~/Documents$ cat secret.yaml
---
apiVersion: v1
kind: Secret
metadata:
    name: mysql-secret
    namespace: default
stringData:
    MYSQL_USER: stonehouse
    MYSQL_PASSWORD: ghorerkhobor
snehashis@sndevice:~/Documents$ sops --encrypt --age $(cat $SOPS_AGE_KEY_FILE |grep -oP "public key: \K(.*)") --encrypted-regex '^(data|stringData)$' --in-place ./secret.yaml
snehashis@sndevice:~/Documents$ cat secret.yaml
apiVersion: v1
kind: Secret
metadata:
    name: mysql-secret
    namespace: default
stringData:
    MYSQL_USER: ENC[AES256_GCM,data:ygI+QaQ9lkpXzA==,iv:mnkyIeFeMfP1bYARJRfzrIOBqoITIegHOBmdzcj4Elk=,tag:WEzxoU0xf48nuYm4qF+cEQ==,type:str]
    MYSQL_PASSWORD: ENC[AES256_GCM,data:PgmJLFU4S5MplE2N,iv:MNm7pqgSV+9tiPlZgmrKZnfUb04tRxKuUIx8uekKefs=,tag:EtEJFIqMS3tXGKiGQSsQ0Q==,type:str]
sops:
    kms: []
    gcp_kms: []
    azure_kv: []
    hc_vault: []
    age:
        - recipient: age10fq6202yhpg3nh0ce32dhz8w64g2xa8lkwux0uzuafnz3ywguuvqqyf68r
          enc: |
            -----BEGIN AGE ENCRYPTED FILE-----
            YWdlLWVuY3J5cHRpb24ub3JnL3YxCi0+IFgyNTUxOSA0VEJJbjhGZkFXOVhpWXpm
            VldRWE1XYmQydThzTGorOTNYU0d4bDVSTzJ3CmRpbE5ld0xTSENSRHR0UUdrcnZw
            SEtGZXpDeWNlS25WWUJ5TkxrbnZPMDgKLS0tIHdwai91NHNzQWJFSmpKT0d0aEli
            dzIxTVlLc1ZxMXJRV3JIajZtdmJBRFkKHsRbvaHghnh1Owrp/lYSVu9huhTT/7EO
            lYgZEDbHhQW79T7eGmKVVzgK47+4zhBYJ2PMijunoNKDlw03b+Ky7g==
            -----END AGE ENCRYPTED FILE-----
    lastmodified: "2025-03-05T08:52:07Z"
    mac: ENC[AES256_GCM,data:bCHl//Lg6toySFl2VSOKr87FubzijPeBiVSNFFIdOXI/T17Hh+hjRqfuQM59j8CaefothhozriNM0VQXgM1UM/HqYMLNxvhNt/j9xSvS1WHX1IMEidR44JVTuckbLtnqyg5XLBnY2Cz53JAAUknUXBElpupZPq+eW/atR+DoDmQ=,iv:9b5TpI6tzD4LebrvvgPVmtfXpQU+W0jMn9MdC3Zjek8=,tag:sRJJ8zMJwBZXxHJnOhkHdw==,type:str]
    pgp: []
    encrypted_regex: ^(data|stringData)$
    version: 3.9.4
snehashis@sndevice:~/Documents$ sops --decrypt --age $(cat $SOPS_AGE_KEY_FILE |grep -oP "public key: \K(.*)") --encrypted-regex '^(data|stringData)$' --in-place ./secret.yaml
snehashis@sndevice:~/Documents$ sops --encrypt --age $(cat $SOPS_AGE_KEY_FILE |grep -oP "public key: \K(.*)") --encrypted-regex '^(data|stringData)$' --in-place ./secret.yaml
```

#### Promql

Metric types

Querying in prometheus

Filtering and label selection

Time series Data

Prometheus functions

Aggregation Operators

Aggregation over time

Histogram queries

Averages and Totals

Label Manipulation

Resource metrics

Data Trends and deriv

Advanced functions