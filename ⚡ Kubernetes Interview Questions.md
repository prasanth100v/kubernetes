# ⚡ Kubernetes Interview Q&A

## ☸️ What is Kubernetes?
 * 🚀 Kubernetes (K8s) is an open-source container orchestration tool used to deploy, scale, and manage containerized applications automatically.
 * 🖥️ Kubernetes Cluster = 👉 Master (Control Plane) + Worker Nodes

## Why do we need Kubernetes❓
```hcl
⚡ Auto scaling of applications
🛠️ Self-healing (restart failed containers)
🌐 Load balancing
🔄 Rolling updates & rollbacks
📊 Efficient resource usage
```

## 📦 What is a Pod?
 * A Pod is the smallest deployable unit in Kubernetes. It can contain one or more containers.

## 🖥️ What is a Node?
 * 📍 A Node is a worker machine (VM or physical) where Pods run.
 * ⚙️ Each node has : `kubelet` & container runtime (`Docker` / containerd) & `kube-proxy`

## 🎛️ What are Control Plane components?
```hcl
🛜 kube-apiserver – Entry point of cluster
💾 etcd – Key-value database (cluster state)
📍 kube-scheduler – Decides where Pods run
⚙️ kube-controller-manager – Handles controllers
```

## 💾 What is etcd?
 * 📌 etcd stores: Cluster configuration & Secrets & Pod states
 * ⚠️ 👉 If etcd is lost → cluster is lost

## 🚀 What is Deployment?
 * Deployment manages: ReplicaSets & Pod lifecycle & Rolling updates and rollbacks

## 🔁 What is ReplicaSet?
 * Ensures desired number of Pods are always running.

## 🌐 What is Service?
 * Service exposes Pods using a `stable IP` & `DNS`.
 * Types: ClusterIP (default) & NodePort & LoadBalancer and ExternalName

## 🗂️ What is Namespace?
 * Namespace is used to logically isolate resources.
 * 🧩 Examples: `default` & `kube-system` & `dev / prod`

## ⚙️ What is ConfigMap?
 * Stores non-sensitive configuration data ***Not encrypted*** (env variables, config files).

## 🔐 What is Secret?
 * Stores sensitive data like: `passwords` & `tokens` and certificates (Base64 encoded)

## 💽 What is Volume?
 * Volume is used for data persistence in Pods.

## 🗄️ What is PersistentVolume (PV)?
 * PV is cluster-level storage resource.

## 📥 What is PersistentVolumeClaim (PVC)?
 * PVC is a request for storage by a Pod.

## 📈 What is HPA?
 * Horizontal Pod Autoscaler automatically scales Pods based on : `CPU & Memory` and `Custom metrics`

## 🛠️ What is Self-Healing in Kubernetes?
 * Kubernetes automatically: Restarts failed containers & Recreates Pods and Reschedules Pods on healthy nodes

## 🌍 What is kube-proxy?
 * Handles network rules and service traffic routing.

## 🌐 What is Ingress?
 * Ingress manages `external HTTP/HTTPS access` to services.

## 🆔 What is Service Account?
 * Used by Pods to authenticate with Kubernetes API.

## 🔒 What is RBAC?
 * Role-Based Access Control controls: Who can access and What actions they can perform

## ⚠️ What is CrashLoopBackOff?
 * Pod keeps crashing and restarting due to: `App error` & `Wrong command` or `Missing config`

## 🎯 What is Taint & Toleration?
 * Taint → Applied on Node & Toleration → Applied on Pod 👉 Controls where Pods can run.

## 📍 What is Node Affinity?
 * Rules to schedule Pods on specific nodes.

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

---

## ⚡ Kubernetes Scenario-Based Interview Q&A
| #️⃣    | ❓ Scenario Question                                     | ✅ Answer                                                               |
| ------ | ------------------------------------------------------- | ---------------------------------------------------------------------- |
| 1️⃣    | 📦 Pod stuck in `Pending` state — what do you check?    | 👉 Node resources, scheduler events, PVC issues, taints/tolerations    |
| 2️⃣    | 🔍 Command to troubleshoot Pending pod?                 | 👉 `kubectl describe pod <pod>`                                        |
| 3️⃣    | 💥 Pod in `CrashLoopBackOff` — common reasons?          | 👉 App crash, wrong env vars, DB failure, missing ConfigMap/Secret     |
| 4️⃣    | 🌐 Pod running but app inaccessible — checks?           | 👉 Service, container port, readiness probe, network policy            |
| 5️⃣    | 🐳 Pod showing `ImagePullBackOff` — why?                | 👉 Wrong image/tag, registry auth issue, network issue                 |
| 6️⃣    | 🔐 How fix private image pull issue?                    | 👉 Create `imagePullSecret`                                            |
| 7️⃣    | 🖥️ Node in `NotReady` state — checks?                  | 👉 kubelet, container runtime, disk/memory pressure, networking        |
| 8️⃣    | 📋 Command to inspect node details?                     | 👉 `kubectl describe node <node>`                                      |
| 9️⃣    | 🔄 New deployment causing failures — action?            | 👉 Rollback deployment                                                 |
| 🔟     | ↩️ Rollback deployment command?                         | 👉 `kubectl rollout undo deployment <name>`                            |
| 1️⃣1️⃣ | 🚧 Deployment stuck during rollout — reasons?           | 👉 Failed probes, insufficient resources, image issues                 |
| 1️⃣2️⃣ | 🌐 Service not reachable inside cluster — checks?       | 👉 Service selector, endpoints, Pod labels                             |
| 1️⃣3️⃣ | 🌍 External users cannot access app — checks?           | 👉 Ingress, LoadBalancer, Security Groups, firewall                    |
| 1️⃣4️⃣ | 🔎 DNS resolution failing in Pods — troubleshooting?    | 👉 Check CoreDNS Pods                                                  |
| 1️⃣5️⃣ | ⚙️ App failing after ConfigMap update — why?            | 👉 Pods may require restart                                            |
| 1️⃣6️⃣ | 🔐 Secret not available inside Pod — checks?            | 👉 Mount path, secret name, namespace mismatch                         |
| 1️⃣7️⃣ | 💾 PVC stuck in `Pending` — reasons?                    | 👉 No StorageClass, insufficient storage, provisioner issue            |
| 1️⃣8️⃣ | 📂 Pod cannot mount volume — checks?                    | 👉 PV/PVC binding, access modes, CSI driver                            |
| 1️⃣9️⃣ | 🚨 Pod getting `OOMKilled` — meaning?                   | 👉 Container exceeded memory limit                                     |
| 2️⃣0️⃣ | 🛠️ Fix `OOMKilled` issue?                              | 👉 Increase memory limit or optimize app                               |
| 2️⃣1️⃣ | 📈 High CPU usage in Pod — troubleshooting?             | 👉 Check app behavior and HPA                                          |
| 2️⃣2️⃣ | 📊 HPA not scaling Pods — reasons?                      | 👉 Metrics server missing, wrong thresholds, missing resource requests |
| 2️⃣3️⃣ | 🌍 Ingress returning `404` — checks?                    | 👉 Host/path rules, backend service, ingress controller                |
| 2️⃣4️⃣ | 🛡️ Pod accessing unauthorized resources — prevention?  | 👉 RBAC + Network Policies                                             |
| 2️⃣5️⃣ | 🚫 Why avoid running containers as root?                | 👉 Security risk                                                       |
| 2️⃣6️⃣ | 📂 Resource not found though created — why?             | 👉 Wrong namespace                                                     |
| 2️⃣7️⃣ | 📋 Command to check all namespaces?                     | 👉 `kubectl get pods -A`                                               |
| 2️⃣8️⃣ | 🗄️ Why is `etcd` critical?                             | 👉 Stores cluster state and configuration                              |
| 2️⃣9️⃣ | 💾 Best practice before cluster upgrade?                | 👉 Take `etcd` backup                                                  |
| 3️⃣0️⃣ | 🚪 API server down — impact?                            | 👉 Cluster management stops; running Pods may continue                 |
| 3️⃣1️⃣ | 📍 Scheduler failure impact?                            | 👉 New Pods won’t schedule                                             |
| 3️⃣2️⃣ | 📊 How monitor Kubernetes cluster?                      | 👉 Prometheus + Grafana                                                |
| 3️⃣3️⃣ | 📜 How collect cluster logs?                            | 👉 EFK stack or Loki                                                   |
| 3️⃣4️⃣ | 🗃️ Database Pod restarted and data lost — why?         | 👉 No Persistent Volume configured                                     |
| 3️⃣5️⃣ | 🏛️ Why use StatefulSet for databases?                  | 👉 Stable identity + persistent storage                                |
| 3️⃣6️⃣ | 🚀 Zero-downtime deployment strategy?                   | 👉 Rolling updates + readiness probes                                  |
| 3️⃣7️⃣ | ⚠️ Pods unavailable during deployment — why?            | 👉 Incorrect readiness/liveness probes                                 |
| 3️⃣8️⃣ | ❤️ Readiness vs Liveness probe?                         | 👉 Readiness = receive traffic, Liveness = restart unhealthy container |
| 3️⃣9️⃣ | 🔄 Liveness probe failing repeatedly — result?          | 👉 Container continuously restarts                                     |
| 4️⃣0️⃣ | 🌐 Pods on different nodes cannot communicate — checks? | 👉 CNI plugin and network routes                                       |
| 4️⃣1️⃣ | 🔎 Service reachable by IP but not DNS — why?           | 👉 CoreDNS issue                                                       |
| 4️⃣2️⃣ | 🔐 User getting `Forbidden` error — checks?             | 👉 RBAC roles and bindings                                             |
| 4️⃣3️⃣ | ⬆️ Worker node upgrade steps?                           | 👉 Drain node → upgrade kubelet → restart → uncordon                   |
| 4️⃣4️⃣ | ☁️ EKS node group not joining cluster — checks?         | 👉 IAM role, Security Groups, bootstrap script                         |
| 4️⃣5️⃣ | 🔄 Argo CD showing `OutOfSync` — meaning?               | 👉 Git state differs from cluster state                                |
| 4️⃣6️⃣ | 🐢 Cluster suddenly slow — what do you check?           | 👉 Node resources, API latency, etcd health, networking                |
| 4️⃣7️⃣ | 🔁 Pods restarting randomly — common causes?            | 👉 OOMKilled, probe failures, node instability                         |
| 4️⃣8️⃣ | 💀 Entire node failed — what happens?                   | 👉 Pods rescheduled to healthy nodes                                   |
| 4️⃣9️⃣ | 🌍 Advantage of Multi-AZ Kubernetes?                    | 👉 High availability                                                   |
| 5️⃣0️⃣ | 📜 View Pod logs command?                               | 👉 `kubectl logs <pod>`                                                |
| 5️⃣1️⃣ | 🖥️ Exec into Pod command?                              | 👉 `kubectl exec -it <pod> -- /bin/sh`                                 |
| 5️⃣2️⃣ | 📋 Check cluster events command?                        | 👉 `kubectl get events`                                                |
| 5️⃣3️⃣ | 🧠 Most important Kubernetes troubleshooting approach?  | 👉 Check Pod → Events → Logs → Node → Networking                       |

## 🔥 Kubernetes Troubleshooting Pro Tips
| ⭐ Best Practice                          | 💡 Why Important                           |
| ---------------------------------------- | ------------------------------------------ |
| 📋 Always check `kubectl describe` first | Shows events and scheduling issues         |
| 📜 Check logs immediately                | Fastest way to identify app errors         |
| 🌐 Learn Kubernetes networking deeply    | Most production issues are network-related |
| ❤️ Understand probes thoroughly          | Prevent downtime and bad deployments       |
| 💾 Verify PVC/PV bindings                | Common storage issue                       |
| 🔄 Practice rollout & rollback           | Essential for production deployments       |
| 🛡️ Use RBAC + Network Policies          | Secure cluster workloads                   |
| 📊 Monitor with Prometheus & Grafana     | Better observability                       |
| ☁️ Use GitOps tools like Argo CD         | Automated deployments and drift detection  |
| 🏛️ Backup `etcd` regularly              | Critical for disaster recovery             |

---

## 🚨 Common Kubernetes Production Issues

| 🔥 **Production Issue**  | 🛠️ **Core Troubleshooting Area** | 🧠 **What It Usually Means**       | 🔍 **First Checks**             | 💡 **Common Root Cause**                |
| ------------------------ | --------------------------------- | ---------------------------------- | ------------------------------- | --------------------------------------- |
| 🔄 **CrashLoopBackOff**  | 📦 Pods                           | Container repeatedly crashing      | `kubectl logs`, `describe pod`  | App crash, bad config, missing env vars |
| ⏳ **Pending Pods**       | 🖥️ Nodes / Scheduler             | Pod cannot be scheduled            | `kubectl describe pod`          | Insufficient CPU/memory, taints         |
| 📦❌ **ImagePullBackOff** | 🌐 Networking / Registry          | Image download failed              | Check image name & secrets      | Wrong image, auth failure               |
| 🌐❌ **DNS Failures**     | 🌍 Networking                     | Services/pods cannot resolve names | Test `nslookup`, CoreDNS logs   | CoreDNS issue, network policy           |
| 💾❌ **PVC Issues**       | 💾 Storage                        | Volume not attached/bound          | `kubectl get pvc,pv`            | Missing StorageClass, capacity issue    |
| ❤️‍🩹 **Probe Failures** | 📦 Pods                           | Health checks failing              | Check liveness/readiness probes | Wrong endpoint, slow startup            |
| 🚨 **Node NotReady**     | 🖥️ Nodes                         | Node unhealthy/unreachable         | `kubectl describe node`         | kubelet down, disk pressure             |
| 📊❌ **Monitoring Gaps**  | 📈 Monitoring                     | Metrics/logs unavailable           | Check Prometheus/Grafana        | Exporter or scrape failure              |
