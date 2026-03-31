# 🏗️ Kubernetes Architecture

Kubernetes follows a **master–worker architecture**.

- The **Control Plane (Master Node)** manages the overall cluster.
- **Worker Nodes** run the containerized applications.

> **“Control Plane makes the decisions, Worker Nodes do the work.”**

---

## 🧠 Control Plane (Master Node)

The Control Plane is the **brain of the Kubernetes cluster**.  
It controls and manages the entire cluster and ensures the desired state is maintained.

### 🔹 Components of Control Plane
### 1️⃣ API Server (`kube-apiserver`)

The **API Server** is the **entry point** for all commands and communications in the cluster.

- Handles requests from:
  - Users
  - Tools (kubectl, dashboards, CI/CD tools)
  - Internal components (Scheduler, Controllers)
- Acts as a **REST API**
- Validates requests (authentication & authorization)
- Stores validated data in **etcd**
- All cluster components communicate **only via the API Server**

📌 Example requests:
- Create Pod
- Delete Service
- Update Deployment

---

### 2️⃣ Scheduler (`kube-scheduler`)

The **Scheduler** decides **where a new Pod should run**.

- Scheduler = **Decision maker**
- It **does not run pods**
- Evaluates:
  - CPU & memory availability
  - Node labels & taints
  - Affinity / anti-affinity rules
- After decision:
  - The Pod is assigned to a node
  - Kubelet on that node runs the Pod

---

### 3️⃣ Controller Manager (`kube-controller-manager`)

The **Controller Manager** runs multiple controllers to ensure the **desired state** is always maintained.

- Continuously watches cluster state via API Server
- Takes corrective action if actual state ≠ desired state
- Runs a **control loop** (non-stop)

📌 Example:
- Desired replicas = 3
- One Pod crashes
- Controller creates a new Pod automatically

#### Key Controllers:
- Node Controller
- Replication Controller
- Deployment Controller
- Job Controller

> Think of it as a **quality inspector** that fixes problems automatically.

---

### 4️⃣ etcd

**etcd** is the **data store of Kubernetes**.

- Distributed **key-value store**
- Stores:
  - Pods
  - Deployments
  - Services
  - ConfigMaps
  - Secrets
  - Cluster configuration
- Uses **Raft consensus algorithm**
- Ensures **consistency and reliability**
- Only **API Server** can talk to etcd directly

📌 Scheduler and Controllers **never access etcd directly**.

---

### 5️⃣ Cloud Controller Manager (Optional)

The **Cloud Controller Manager** allows Kubernetes to interact with **cloud provider APIs**.

Supports:
- Load Balancers (AWS ELB, Azure LB, GCP LB)
- Persistent Volumes (EBS, Azure Disk, GCP PD)
- Node lifecycle management

📌 Example:
- Automatically removes nodes deleted in the cloud
- Creates cloud load balancers for Services

---

## ⚙️ Worker Nodes

A **Worker Node** is where applications actually run.

Each worker node contains:

- **Kubelet**
- **kube-proxy**
- **Container Runtime**

---

### 🔹 Kubelet

**Kubelet** is an agent running on every worker node.

- Communicates with API Server
- Ensures Pods assigned to the node are:
  - Running
  - Healthy
- Restarts containers based on restart policy

#### 📦 Kubelet Workflow

1. Pod scheduled to a node by Scheduler
2. Kubelet receives Pod details from API Server
3. Pulls container image
4. Starts container via runtime
5. Reports status back to API Server
6. Restarts container if it crashes

---

### 🔹 kube-proxy

**kube-proxy** handles **network routing and service communication**.
**kube-proxy** is a component that: Runs on every node, ***Manages network rules*** and Routes traffic to the correct Pod (via Services)

#### How kube-proxy Works:

1. Watches API Server for Services and Endpoints
2. Updates routing rules on each node
3. Uses:
   - **iptables** or **IPVS**
   - ***iptables*** 
   - Uses Linux iptables rules (Client → Service IP → iptables rules → Pod) Default in Kubernetes; No extra setup required
   - ***IPVS***
   - Uses Linux IPVS (IP Virtual Server) (Client → Service IP → IPVS load balancer → Pod) Supports advanced load balancing ; High performance and scalability
4. Load-balances traffic across healthy Pods
5. Routes traffic across nodes if needed

📌 Example:
- Request hits Service IP on Node A
- Pod is running on Node B
- kube-proxy forwards traffic to Node B

---

### 🔹 Container Runtime

The **Container Runtime** actually runs the containers.

Examples:
- Docker
- containerd
- CRI-O

#### How it Works:

1. Kubelet requests container start
2. Runtime pulls image from registry (Docker Hub, ECR, etc.)
3. Starts and manages the container
4. Reports container status back to Kubelet

---

## ✅ Summary

- **Control Plane** manages the cluster
- **Worker Nodes** run applications
- API Server is the communication hub
- etcd stores all cluster data
- Scheduler decides placement
- Controllers enforce desired state
- Kubelet runs and monitors Pods
- kube-proxy handles networking
- Container Runtime runs containers

---
