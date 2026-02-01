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







