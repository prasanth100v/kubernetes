# 🚀 Advanced Scheduling in Kubernetes (Easy + Complete Guide)

---

## 🌟 What is Scheduling in Kubernetes?

The **Kubernetes Scheduler** decides:

👉 Which node will run your Pod  

By default, it uses:
- CPU availability  
- Memory availability  

---

## 🎯 What is Advanced Scheduling?

👉 Advanced scheduling = **Control how Pods are placed on nodes**

Using features like:
- Node Affinity / Anti-affinity  
- Pod Affinity / Anti-affinity  
- Taints and Tolerations  
- Topology Spread Constraints  
- Priority & Preemption  

---

# 🧠 Core Scheduling Concepts

---

## 🔹 nodeSelector (Basic Scheduling)

👉 Simplest way to assign Pods to nodes

✔ Uses key-value labels  

### 📌 Example Use:
- Run only on GPU nodes  

---

## 🔹 nodeAffinity (Advanced nodeSelector)

👉 More powerful and flexible than nodeSelector  

✔ Supports:
- Hard rules (required)  
- Soft rules (preferred)  

### 📌 Example:
- Must run on SSD nodes  
- Prefer zone-a  

---

## 🔹 podAffinity (Co-locate Pods)

👉 Schedule Pods **close to other Pods**

✔ Used for:
- Low latency communication  

### 📌 Example:
- Place frontend near backend  

---

## 🔹 podAntiAffinity (Spread Pods)

👉 Schedule Pods **away from each other**

✔ Used for:
- High Availability  

### 📌 Example:
- Spread replicas across nodes  

---

## 🔹 topologyKey

👉 Defines grouping for scheduling  

### 📌 Common Keys:
- kubernetes.io/hostname → Node level  
- topology.kubernetes.io/zone → Zone level  

---

## 🔹 topologySpreadConstraints

👉 Ensures Pods are evenly distributed  

✔ Prevents:
- All Pods in one zone  

### 📌 Example:
- Spread replicas across zones  

---

## 🔹 Taints (Node Protection)

👉 Prevent Pods from running on a node  

### 📌 Example:
dedicated=ml:NoSchedule  

✔ Only allowed Pods can run  

---

## 🔹 Tolerations (Allow Access)

👉 Allow Pods to run on tainted nodes  

✔ Used for:
- Special workloads  

---

## 🔹 PriorityClass

👉 Assign priority to Pods  

✔ Higher priority → Scheduled first  

---

## 🔹 Preemption

👉 High-priority Pods can:

❌ Evict low-priority Pods  
✔ Free space for critical apps  

---

## 🔹 schedulerName

👉 Use custom scheduler instead of default  

✔ Example:
- Cost-aware scheduler  
- Latency-aware scheduler  

---

## 🔹 Resource Requests & Limits

👉 Help scheduler decide placement  

✔ Prevent:
- Overloading nodes  

---

## 🔹 Default Scheduler

👉 Built-in Kubernetes scheduler  

✔ Used unless custom scheduler specified  

---

## 🔹 Descheduler

👉 Rebalances Pods AFTER scheduling  

✔ Use case:
- Node changes  
- Cluster scaling  

---

## 🔹 DaemonSet

👉 Ensures one Pod runs on every node  

✔ Used for:
- Logging agents  
- Monitoring  

---

# 🧪 Real-Life Examples

- GPU workloads → nodeSelector / taints  
- Microservices → podAffinity  
- HA systems → podAntiAffinity  
- Multi-zone apps → topologySpreadConstraints  

---

# 🔧 Important Commands

---

## 🔍 Check Node Labels

kubectl get nodes --show-labels  

---

## 🏷 Add Label to Node

kubectl label nodes <node-name> disktype=ssd  

---

## 📍 Assign Zone Label

kubectl label nodes <node-name> zone=us-east-1a  

---

## 🚫 Taint a Node

kubectl taint nodes <node-name> dedicated=ml:NoSchedule  

---

## 🔍 Check Taints

kubectl describe node <node-name> | grep Taints  

---

## 🔁 Watch Events (Real-Time)

kubectl get events --watch  

---

## 🔎 Check Pod Scheduling Info

kubectl describe pod <pod-name>  

---

# ⚖️ Summary Table

| Feature                  | Purpose                          |
|------------------------|----------------------------------|
| nodeSelector           | Simple node selection            |
| nodeAffinity           | Advanced node rules              |
| podAffinity            | Place pods together              |
| podAntiAffinity        | Spread pods                      |
| taints                 | Block pods                       |
| tolerations            | Allow specific pods              |
| topologySpread         | Even distribution                |
| priorityClass          | Priority scheduling              |
| preemption             | Evict lower priority pods        |

---

# 🧠 Quick Revision

- nodeSelector → Simple placement  
- nodeAffinity → Advanced rules  
- podAffinity → Together  
- podAntiAffinity → Separate  
- taints → Block  
- tolerations → Allow  
- topologySpread → Balance  
- priority → Importance  

---

# 🎯 One-Line Answer

Advanced scheduling in Kubernetes allows precise control over Pod placement using affinity rules, taints, topology, and priority mechanisms.
