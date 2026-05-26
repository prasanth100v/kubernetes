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

---

## ☸️ Kubernetes (K8s) — Rapid Fire Interview Q&A

| #️⃣    | ❓ Question                                       | ✅ Answer                                                                                                        |
| ------ | ------------------------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| 1️⃣    | ☸️ What is Kubernetes?                           | 👉 Open-source container orchestration platform for deploying, scaling, and managing containerized applications |
| 2️⃣    | 🏗️ Kubernetes Cluster consists of?              | 👉 Control Plane + Worker Nodes                                                                                 |
| 3️⃣    | 🚀 Why do we need Kubernetes?                    | 👉 Auto scaling, self-healing, load balancing, rolling updates, efficient resource usage                        |
| 4️⃣    | 📦 What is a Pod?                                | 👉 Smallest deployable unit in Kubernetes containing one or more containers                                     |
| 5️⃣    | 🖥️ What is a Node?                              | 👉 Worker machine where Pods run                                                                                |
| 6️⃣    | ⚙️ Components present in a Node?                 | 👉 kubelet, container runtime, kube-proxy                                                                       |
| 7️⃣    | 🎯 What is kubelet?                              | 👉 Agent running on each node managing Pods                                                                     |
| 8️⃣    | 🔄 What is container runtime?                    | 👉 Software that runs containers like Docker/containerd                                                         |
| 9️⃣    | 🌐 What is kube-proxy?                           | 👉 Handles networking and service traffic routing                                                               |
| 🔟     | 🧠 What are Control Plane components?            | 👉 kube-apiserver, etcd, kube-scheduler, kube-controller-manager                                                |
| 1️⃣1️⃣ | 🚪 What is kube-apiserver?                       | 👉 Entry point for Kubernetes cluster communication                                                             |
| 1️⃣2️⃣ | 🗄️ What is etcd?                                | 👉 Distributed key-value database storing cluster state                                                         |
| 1️⃣3️⃣ | ⚠️ What happens if etcd is lost?                 | 👉 Cluster state is lost                                                                                        |
| 1️⃣4️⃣ | 📍 What is kube-scheduler?                       | 👉 Decides which node Pods should run on                                                                        |
| 1️⃣5️⃣ | 🎛️ What is kube-controller-manager?             | 👉 Runs controllers managing cluster state                                                                      |
| 1️⃣6️⃣ | 📦 What is Deployment?                           | 👉 Manages ReplicaSets, Pods, updates, and rollbacks                                                            |
| 1️⃣7️⃣ | 🔁 What is ReplicaSet?                           | 👉 Ensures desired number of Pods are running                                                                   |
| 1️⃣8️⃣ | 🌐 What is a Service?                            | 👉 Stable networking endpoint for accessing Pods                                                                |
| 1️⃣9️⃣ | 🔌 Types of Kubernetes Services?                 | 👉 ClusterIP, NodePort, LoadBalancer, ExternalName                                                              |
| 2️⃣0️⃣ | 🏠 What is ClusterIP?                            | 👉 Internal-only service access inside cluster                                                                  |
| 2️⃣1️⃣ | 🌍 What is NodePort?                             | 👉 Exposes service on node port externally                                                                      |
| 2️⃣2️⃣ | ☁️ What is LoadBalancer Service?                 | 👉 Exposes service using cloud provider load balancer                                                           |
| 2️⃣3️⃣ | 🔗 What is ExternalName Service?                 | 👉 Maps service to external DNS name                                                                            |
| 2️⃣4️⃣ | 🗂️ What is Namespace?                           | 👉 Logical isolation for Kubernetes resources                                                                   |
| 2️⃣5️⃣ | 📂 Common namespaces?                            | 👉 default, kube-system, dev, prod                                                                              |
| 2️⃣6️⃣ | ⚙️ What is ConfigMap?                            | 👉 Stores non-sensitive configuration data                                                                      |
| 2️⃣7️⃣ | 🔐 What is Secret?                               | 👉 Stores sensitive information like passwords/tokens                                                           |
| 2️⃣8️⃣ | 🔒 Are Kubernetes Secrets encrypted by default?  | 👉 Base64 encoded, not fully encrypted unless encryption enabled                                                |
| 2️⃣9️⃣ | 💾 What is Volume?                               | 👉 Persistent/shared storage for Pods                                                                           |
| 3️⃣0️⃣ | 🗄️ What is PersistentVolume (PV)?               | 👉 Cluster-level storage resource                                                                               |
| 3️⃣1️⃣ | 📥 What is PersistentVolumeClaim (PVC)?          | 👉 Request for storage by Pod                                                                                   |
| 3️⃣2️⃣ | 📈 What is HPA?                                  | 👉 Horizontal Pod Autoscaler for automatic scaling                                                              |
| 3️⃣3️⃣ | ❤️ What is Self-Healing in Kubernetes?           | 👉 Automatically restarts/reschedules failed Pods                                                               |
| 3️⃣4️⃣ | 🌍 What is Ingress?                              | 👉 Manages external HTTP/HTTPS traffic routing                                                                  |
| 3️⃣5️⃣ | 👤 What is Service Account?                      | 👉 Identity used by Pods to access Kubernetes API                                                               |
| 3️⃣6️⃣ | 🛡️ What is RBAC?                                | 👉 Role-Based Access Control for permissions                                                                    |
| 3️⃣7️⃣ | 💥 What is CrashLoopBackOff?                     | 👉 Pod repeatedly crashing and restarting                                                                       |
| 3️⃣8️⃣ | 🚫 Common reasons for CrashLoopBackOff?          | 👉 App error, wrong command, missing config                                                                     |
| 3️⃣9️⃣ | ⚖️ What is Taint?                                | 👉 Restriction applied on nodes                                                                                 |
| 4️⃣0️⃣ | ✅ What is Toleration?                            | 👉 Allows Pod to run on tainted node                                                                            |
| 4️⃣1️⃣ | 📍 What is Node Affinity?                        | 👉 Scheduling rule for specific nodes                                                                           |
| 4️⃣2️⃣ | 🔄 What is Rolling Update?                       | 👉 Gradual application update without downtime                                                                  |
| 4️⃣3️⃣ | ↩️ What is Rollback?                             | 👉 Revert to previous stable deployment version                                                                 |
| 4️⃣4️⃣ | 📊 What is kubectl?                              | 👉 CLI tool to manage Kubernetes clusters                                                                       |
| 4️⃣5️⃣ | 📋 Command to view Pods?                         | 👉 `kubectl get pods`                                                                                           |
| 4️⃣6️⃣ | 📋 Command to view nodes?                        | 👉 `kubectl get nodes`                                                                                          |
| 4️⃣7️⃣ | 🔍 Command to describe Pod?                      | 👉 `kubectl describe pod <pod-name>`                                                                            |
| 4️⃣8️⃣ | 📜 Command to check Pod logs?                    | 👉 `kubectl logs <pod-name>`                                                                                    |
| 4️⃣9️⃣ | 🔄 Restart Deployment command?                   | 👉 `kubectl rollout restart deployment <name>`                                                                  |
| 5️⃣0️⃣ | 📈 Scale Deployment command?                     | 👉 `kubectl scale deployment app --replicas=5`                                                                  |
| 5️⃣1️⃣ | 🌐 Command to expose deployment?                 | 👉 `kubectl expose deployment app --port=80`                                                                    |
| 5️⃣2️⃣ | 🧪 Command to test cluster info?                 | 👉 `kubectl cluster-info`                                                                                       |
| 5️⃣3️⃣ | 📂 Command to switch namespace?                  | 👉 `kubectl config set-context --current --namespace=dev`                                                       |
| 5️⃣4️⃣ | 🔍 How check events in cluster?                  | 👉 `kubectl get events`                                                                                         |
| 5️⃣5️⃣ | 🚑 Pod stuck Pending — reasons?                  | 👉 Insufficient resources or scheduling issues                                                                  |
| 5️⃣6️⃣ | ❌ ImagePullBackOff means?                        | 👉 Kubernetes unable to pull container image                                                                    |
| 5️⃣7️⃣ | 🌐 Service not reachable — checks?               | 👉 Service type, endpoints, Pod status, network policies                                                        |
| 5️⃣8️⃣ | 🔄 Why use Deployments instead of Pods directly? | 👉 Automatic scaling, updates, and recovery                                                                     |
| 5️⃣9️⃣ | ☁️ Kubernetes commonly used with?                | 👉 Amazon Web Services EKS, GKE, AKS                                                                            |
| 6️⃣0️⃣ | 🚀 Why Kubernetes popular in DevOps?             | 👉 Scalability, automation, self-healing, portability                                                           |

---

## 🔥 Kubernetes Interview Gold Tips
| ⭐ Best Practice                  | 💡 Why Important               |
| -------------------------------- | ------------------------------ |
| 🔐 Use RBAC                      | Secure cluster access          |
| 📂 Use Namespaces                | Environment isolation          |
| ❤️ Enable probes                 | Health monitoring              |
| 📈 Use HPA                       | Automatic scaling              |
| 🔄 Use Deployments               | Safer updates & rollbacks      |
| 🌐 Use Ingress                   | Centralized traffic management |
| 💾 Use PVCs                      | Persistent application storage |
| 🛡️ Store secrets securely       | Protect sensitive data         |
| 📊 Monitor cluster               | Better observability           |
| ☸️ Use GitOps tools like Argo CD | Automated deployments          |



