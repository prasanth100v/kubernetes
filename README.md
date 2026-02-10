## 📘 Kubernetes Glossary
| Term                            | What it Means                    | Simple Explanation / Example                           |
| ------------------------------- | -------------------------------- | ------------------------------------------------------ |
| **Kubernetes**                  | Container orchestration platform | Manages containers automatically (deploy, scale, heal) |
| **Cluster**                     | Group of machines                | One Kubernetes setup = multiple servers                |
| **Node**                        | Worker machine                   | A server where pods run                                |
| **Control Plane**               | Brain of cluster                 | Decides *what runs where*                              |
| **Worker Node**                 | Execution machine                | Runs application containers                            |
| **Pod**                         | Smallest deployable unit         | One or more containers running together                |
| **Container**                   | App runtime                      | Runs your app (Docker container)                       |
| **Image**                       | App blueprint                    | Used to create containers                              |
| **Namespace**                   | Logical isolation                | Separate environments like `dev`, `prod`               |
| **Deployment**                  | App manager                      | Ensures correct number of pods                         |
| **ReplicaSet**                  | Pod counter                      | Keeps desired pod count                                |
| **Service**                     | Network access                   | Exposes pods internally or externally                  |
| **ClusterIP**                   | Internal service                 | Access only inside cluster                             |
| **NodePort**                    | External service                 | Exposes app using node IP + port                       |
| **LoadBalancer**                | Cloud service                    | Exposes app with public IP                             |
| **Ingress**                     | HTTP routing                     | Routes traffic using domain/path                       |
| **Ingress Controller**          | Traffic handler                  | Implements ingress rules                               |
| **ConfigMap**                   | Non-secret config                | Stores env variables, configs                          |
| **Secret**                      | Sensitive data                   | Stores passwords, tokens                               |
| **Volume**                      | Storage                          | Data shared with containers                            |
| **PersistentVolume (PV)**       | Actual storage                   | Disk provided by admin/cloud (storage volume that is used to persist application data.)  |
| **PersistentVolumeClaim (PVC)** | Storage request                  | Pod asks for storage  (request for storage)            |
| **StatefulSet**                 | Stateful apps                    | For DBs like MySQL                                     |
| **DaemonSet**                   | One pod per node                 | Log agents, monitoring                                 |
| **Job**                         | One-time task                    | Runs once (backup, batch job)                          |
| **CronJob**                     | Scheduled job                    | Runs jobs at fixed time                                |
| **Label**                       | Key-value tag                    | Used to select objects                                 |
| **Selector**                    | Matching rule                    | Connects service to pods                               |
| **Taint**                       | Node restriction                 | Prevents pod scheduling                                |
| **Toleration**                  | Taint override                   | Allows pod on tainted node                             |
| **Affinity**                    | Pod placement                    | Controls where pods run  (✅ “Place this Pod here / near this.”)           |
| **Pod Anti-Affinity**           | Separation rule  ( improves availability )   | Spread pods across nodes  (❌ “Do NOT place this Pod on the same node (or zone)) |
| **HPA**                         | Auto scaling                     | Scales pods based on CPU                               |
| **Rolling Update**              | Zero downtime deploy             | Updates pods gradually                                 |
| **kubectl**                     | CLI tool                         | Command line for Kubernetes                            |
| **etcd**                        | Cluster database                 | Stores cluster state                                   |
| **Scheduler**                   | Pod placer                       | Chooses node for pod                                   |
| **Controller Manager**          | Maintainer                       | Keeps desired state                                    |
| **API Server**                  | Entry point                      | All requests go through this                           |
| **Helm**                        | Package manager                  | Installs apps using charts                             |
| **Chart**                       | Helm package                     | Predefined Kubernetes resources                        |
| **CRD**                         | Custom resource                  | Add your own resource type                             |
| **Operator**                    | Smart controller                 | Manages complex apps                                   |
| **Probe (Liveness)**            | Health check                     | Restarts unhealthy pod                                 |
| **Probe (Readiness)**           | Traffic check                    | Controls traffic flow                                  |
| **Probe (Startup)**             | Boot check                       | For slow-starting apps                                 |

## 📗 Advanced Kubernetes Glossary
| Term                            | What it Is            | Simple Explanation / Real Use          |
| ------------------------------- | --------------------- | -------------------------------------- |
| **Kubernetes API Group**        | Resource category (API groups make it easier to extend the Kubernetes API) to solve specific user needs.| Example: `apps/v1`, `batch/v1`|
| **API Version**                 | Resource version      | Example: `apps/v1` for Deployment      |
| **Manifest**                    | YAML file             | Describes Kubernetes objects           |
| **Desired State**               | Target configuration  | What you *want* Kubernetes to maintain |
| **Current State**               | Actual condition      | What is currently running              |
| **Reconciliation Loop**         | Continuous check      | K8s keeps fixing differences           |
| **Admission Controller**        | Request validator     | Allows or denies API requests          |
| **Mutating Webhook**            | Modifies requests     | Adds labels automatically              |
| **Validating Webhook**          | Validates rules       | Blocks wrong configs                   |
| **CNI**                         | Networking plugin     | Calico, Flannel                        |
| **CSI**                         | (Container Storage Interface)  Storage plugin        | Connects storage systems               |
| **kube-proxy**                  | Network manager       | Enables service networking             |
| **Endpoint**                    | Pod IP list           | Actual backend pods                    |
| **EndpointSlice**               | Scalable endpoints    | Improved endpoint handling             |
| **Service Mesh**                | Traffic control       | Istio, Linkerd                         |
| **Sidecar**                     | Helper container      | Logging, proxy container               |
| **Init Container**              | Pre-run container     | Runs before app starts                 |
| **Eviction**                    | Pod removal           | Due to memory/disk pressure            |
| **OOMKilled**                   | Memory crash          | Pod killed due to RAM limit            |
| **Resource Request**            | Minimum resource      | Guaranteed CPU/memory                  |
| **Resource Limit**              | Max resource          | Prevents overuse                       |
| **QoS Class**                   | Priority level (Quality of Service (QoS) classes)   | to decide which Pods to evict from a Node experiencing Node Pressure.   |
| **Node Pressure**               | Resource shortage     | Memory, disk, PID pressure             |
| **Pod Disruption Budget (PDB)** | Availability rule     | Limits pod down and ensuring high availability in a Kubernetes during node maintenance, cluster upgrades |
| **Leader Election**             | Master selection      | Used by controllers                    |
| **Garbage Collection**          | Auto cleanup          | Deletes unused resources               |
| **Custom Controller**           | Custom logic          | Watches CRDs                           |
| **Operator Pattern**            | App automation        | DB lifecycle management                |
| **Blue-Green Deployment**       | Zero-risk release     | Switch traffic instantly               |
| **Canary Deployment**           | Gradual rollout       | Test with few users                    |
| **Node Drain**                  | Safe node maintenance | Moves pods before shutdown             |
| **Cordoning**                   | Block scheduling      | Prevent new pods on node               |
| **Static Pod**                  | Node-level pod        | Managed by kubelet only                |
| **Mirror Pod**                  | API reflection        | Static pod visible in API              |
| **RBAC**                        | Access control        | Who can do what (define roles and permissions for users, groups, or service accounts.)     |
| **Role**                        | Namespace permission  | Limited access                         |
| **ClusterRole**                 | Cluster-wide access   | Admin-level permissions                |
| **ServiceAccount**              | Pod identity Used for API access (non-human account) |   ServiceAccount is an identity used by applications (Pods) to talk securely to the Kubernetes API or other external services.            |
| **Token**                       | Authentication key    | Used by pods (token is a credential used to authenticate to the Kubernetes API server.)    |
| **Audit Log**      | Activity log (CCTV for your cluster API 🔍)  | Audit Logs record WHO did WHAT, WHEN, WHERE, and RESULT for every request made to the API Server.   |
| **NodeSelector**                | Simple placement      | Run pod on specific node ( ✅ “Run this Pod only on nodes with these labels.” ) |


## 📘 AWS EKS (Elastic Kubernetes Service) Glossary
| Term                                    | What It Is                | Simple Explanation / Real Use              |
| --------------------------------------- | ------------------------- | ------------------------------------------ |
| **Amazon Web Services**                 | Cloud platform            | Provides infrastructure & managed services |
| **Amazon EKS**                          | Managed Kubernetes        | AWS manages control plane                  |
| **EKS Cluster**                         | Kubernetes cluster        | Runs Kubernetes on AWS                     |
| **Control Plane**                       | Managed by AWS            | API server, etcd, scheduler                |
| **Worker Node**                         | EC2 instance              | Runs application pods                      |
| **Managed Node Group**                  | AWS-managed nodes         | AWS handles upgrades                       |
| **Self-Managed Node Group**             | User-managed nodes        | Full control over EC2                      |
| **Fargate**                             | Serverless compute        | Run pods without EC2                       |
| **EKS Add-ons**                         | Managed components        | CoreDNS, kube-proxy                        |
| **Cluster Endpoint**                    | API endpoint              | kubectl connects here                      |
| **IAM Role**                            | AWS identity              | Controls access to AWS                     |
| **IAM Role for Service Account (IRSA)** | Pod-level IAM             | Secure AWS access for pods                 |
| **aws-auth ConfigMap**                  | Auth mapping              | Maps IAM users to RBAC                     |
| **OIDC Provider**                       | Identity provider         | Enables IRSA                               |
| **VPC**                                 | Network boundary          | Isolates EKS cluster                       |
| **Subnets**                             | Network segments          | Public / private nodes                     |
| **Security Group**                      | Firewall rules            | Controls traffic                           |
| **ENI**                                 | Elastic Network Interface | Pod networking                             |
| **VPC CNI**                             | AWS network plugin        | Assigns VPC IPs to pods                    |
| **Pod IP**                              | VPC IP                    | Pods get real AWS IP                       |
| **Service IP**                          | Virtual IP                | Internal cluster access                    |
| **LoadBalancer Service**                | AWS ELB                   | Creates ALB/NLB                            |
| **ALB Ingress Controller**              | Ingress handler           | Uses AWS ALB                               |
| **NLB**                                 | Network Load Balancer     | High performance                           |
| **EBS CSI Driver**                      | Block storage             | Persistent volumes                         |
| **EFS CSI Driver**                      | Shared storage            | Multiple pods share files                  |
| **PersistentVolume (PV)**               | Actual storage            | Backed by EBS/EFS                          |
| **PersistentVolumeClaim (PVC)**         | Storage request           | Pod asks for storage                       |
| **Cluster Autoscaler**                  | Node scaling              | Adds/removes nodes                         |
| **HPA**                                 | Pod autoscaling           | CPU/memory based                           |
| **Karpenter**                           | Smart autoscaler          | Fast node provisioning                     |
| **CloudWatch Logs**                     | Logging                   | Centralized logs                           |
| **Container Insights**                  | Monitoring                | CPU, memory metrics                        |
| **ECR**                                 | Container registry        | Stores Docker images                       |
| **EKS Upgrade**                         | Version update            | AWS upgrades control plane                 |
| **Node AMI**                            | OS image                  | Used by worker nodes                       |
| **eksctl**                              | CLI tool                  | Creates EKS clusters                       |
| **kubectl**                             | Kubernetes CLI            | Manage resources                           |
| **Helm on EKS**                         | Package manager           | App deployments                            |
| **Private Cluster**                     | No public endpoint        | Secure cluster access                      |
| **Public Endpoint**                     | Internet accessible       | Easier access                              |
| **Endpoint Access Control**             | Security setting          | Public / private / both                    |
| **Multi-AZ**                            | High availability         | Nodes across AZs                           |
| **EKS Pricing**                         | Cluster cost              | Per-hour cluster fee                       |

---

# 🧰 Kubernetes Tools & Core Concepts

## Minikube
**Minikube** is a tool that allows you to run a **single-node Kubernetes cluster locally** on your laptop or desktop. 
Developers use Minikube to test applications in a Kubernetes environment before deploying them to real clusters.

---

## kubeadm
**kubeadm** is a tool that helps you set up a Kubernetes cluster quickly and easily.  
It is commonly used to set up Kubernetes on:

- Physical servers
- Virtual machines
- Local data centers
- Cloud VMs such as:
  - AWS EC2
  - Google Cloud Compute Engine
  - Azure Virtual Machines

---

## Kubernetes API Server Port
**Port 6443** is the default port used by the Kubernetes API Server.  
When any component (such as kubelet, kubectl, or kube-proxy) needs to communicate with the control plane, it connects to the API Server on **port 6443**.
> 📌 If port 6443 is blocked, the cluster will not function properly.

---

## 🕸️ Calico (CNI Plugin) (Container Network Interface)
**CNI** is responsible for pod-to-pod networking in Kubernetes.

What CNI Does:
- Assigns IP addresses to Pods
- Enables Pod-to-Pod communication

**Calico** is a simple, fast, and powerful networking plugin for Kubernetes.  
📌 Calico is often chosen when security and performance are top priorities.
It is widely used in production environments because it provides:

- Pod-to-pod networking
- High-performance networking
- Network Policies for security
- Works on cloud and on-prem environments

