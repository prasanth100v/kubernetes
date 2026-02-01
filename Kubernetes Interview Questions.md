## What is Kubernetes?
Kubernetes (K8s) is an open-source container orchestration tool used to deploy, scale, and manage containerized applications automatically.
> Kubernetes Cluster = 👉 Master (Control Plane) + Worker Nodes
## Why do we need Kubernetes?
```
Auto scaling of applications
Self-healing (restart failed containers)
Load balancing
Rolling updates & rollbacks
Efficient resource usage
```
## What is a Pod?
A Pod is the smallest deployable unit in Kubernetes. It can contain one or more containers.
## What is a Node?
A Node is a worker machine (VM or physical) where Pods run.
> Each node has: kubelet & container runtime (Docker / containerd) & kube-proxy
## What are Control Plane components?
```
kube-apiserver – Entry point of cluster
etcd – Key-value database (cluster state)
kube-scheduler – Decides where Pods run
kube-controller-manager – Handles controllers
```
## What is etcd?
etcd stores: Cluster configuration & Secrets & Pod states
> 👉 If etcd is lost → cluster is lost
## What is Deployment?
Deployment manages: ReplicaSets & Pod lifecycle & Rolling updates and rollbacks
## What is ReplicaSet?
Ensures desired number of Pods are always running.
## What is Service?
Service exposes Pods using a stable IP & DNS.
> Types: ClusterIP (default) & NodePort & LoadBalancer and ExternalName
## What is Namespace?
Namespace is used to logically isolate resources.
> Examples: default & kube-system & dev / prod










