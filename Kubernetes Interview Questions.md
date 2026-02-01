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
## What is ConfigMap?
Stores non-sensitive configuration data ***Not encrypted*** (env variables, config files).
## What is Secret?
Stores sensitive data like: passwords & tokens and certificates (Base64 encoded)
## What is Volume?
Volume is used for data persistence in Pods.
## What is PersistentVolume (PV)?
PV is cluster-level storage resource.
## What is PersistentVolumeClaim (PVC)?
PVC is a request for storage by a Pod.
## What is HPA?
Horizontal Pod Autoscaler automatically scales Pods based on: CPU & Memory and Custom metrics
## What is Self-Healing in Kubernetes?
Kubernetes automatically: Restarts failed containers & Recreates Pods and Reschedules Pods on healthy nodes
## What is kube-proxy?
Handles network rules and service traffic routing.
## What is Ingress?
Ingress manages external HTTP/HTTPS access to services.
## What is Service Account?
Used by Pods to authenticate with Kubernetes API.
## What is RBAC?
Role-Based Access Control controls: Who can access and What actions they can perform
## What is CrashLoopBackOff?
Pod keeps crashing and restarting due to: App error & Wrong command or Missing config
## What is Taint & Toleration?
Taint → Applied on Node & Toleration → Applied on Pod 👉 Controls where Pods can run.
## What is Node Affinity?
Rules to schedule Pods on specific nodes.








