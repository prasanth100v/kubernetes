# 🚀 Advanced Scheduling in Kubernetes 
## 🌟 What is Scheduling in Kubernetes?
The **Kubernetes Scheduler** decides: 👉 Which node will run your Pod  

#### ⚙️ Default Scheduling Behavior
By default, Kubernetes scheduler selects nodes based on:

- 🧮 CPU availability
- 💾 Memory availability
- 📦 Resource requests

➡️ This is basic scheduling
---

## 🎯 What is Advanced Scheduling?

👉 Advanced scheduling = **Control how Pods are placed on nodes**

#### Instead of letting Kubernetes decide randomly, you can:

- 🎯 Force Pods to specific nodes
- ⚖️ Spread Pods evenly
- 🚫 Avoid certain nodes
- 🧩 Place Pods close or far from each other

Using features like:
- Node Affinity / Anti-affinity  
- Pod Affinity / Anti-affinity  
- Taints and Tolerations  
- Topology Spread Constraints  
- Priority & Preemption  

---

# 🧠 Core Scheduling Concepts
## 1. nodeSelector (🟢 Simple Scheduling)
👉 Simplest way to assign Pods to nodes ✔ Uses key-value labels  

### 📌 Example Use:
```
nodeSelector:
  disktype: ssd
```
- 💡 Use Case: Run only on 🎮 GPU nodes and 💽 SSD nodes
- ⚠️ Limitation: Only supports exact match, No flexibility

---

## 2. nodeAffinity (🎯 Advanced Node Selection)

👉 More powerful and flexible than nodeSelector  

✔ Supports:
- Hard rules (required)  
- Soft rules (preferred)  

###🔥 Types:
| Type                                            | Behavior     |
| ----------------------------------------------- | ------------ |
| requiredDuringSchedulingIgnoredDuringExecution  | MUST match   |
| preferredDuringSchedulingIgnoredDuringExecution | TRY to match |

---

## 3. Pod Affinity (🤝 Stay Together)

👉 Schedule Pods **close to other Pods**

✔ Used for:
- Low latency communication  

### 📌 Example:
- Frontend + Backend in same node/zone  

---

## 4. Pod Anti-Affinity (🚫 Stay Apart)

👉 Schedule Pods **away from each other**

✔ Used for:
- High Availability  

### 📌 Example:
- 💡 Use Case: Spread replicas across nodes → High Availability

---

## 5. topologyKey (🌍 Grouping Logic)

👉 Defines grouping for scheduling  

### 📌 Common Keys:
- kubernetes.io/hostname → Node level  
- topology.kubernetes.io/zone → Zone level  

---

## 6. Topology Spread Constraints (⚖️ Even Distribution)

👉 Ensures Pods are evenly distributed  , Spread replicas across zones 

✔ Prevents:
- All Pods in one zone  

### 💡 Benefit:
- Prevent all pods in one zone ❌
- Improves HA ✅
 

---

## 7. Taints & Tolerations (🚫 + 🔓 Control Access)
#### 🚫 Taints (Node Protection)
👉 Prevent Pods from scheduling on a node

### 📌 Example:
```
kubectl taint nodes node1 dedicated=ml:NoSchedule
```
✔ Only allowed Pods can run  

---

## 🔓 Tolerations (Pod Permission)
👉 Allow Pods to run on tainted nodes

```
tolerations:
- key: "dedicated"
  operator: "Equal"
  value: "ml"
  effect: "NoSchedule"
```
#### 💡 Use Case:
Isolate:
- ML workloads 🤖
- Infra nodes 🏗️

---

## 8. Priority Classes & Preemption (🔥 Critical Pods First)
#### 🏆 Priority Class

- 👉 Assign priority (importance) to Pods
- ✔ Higher priority → Scheduled first  

---

## ⚡ Preemption
👉 High-priority Pod can:

- ❌ Evict lower-priority Pods
- 📦 Take their resources

💡 Use Case:
- Production > Testing
- Critical apps always run
---

## 9. schedulerName (🧠 Custom Scheduler)

👉 Use custom scheduler instead of default  
```
schedulerName: my-custom-scheduler
```
💡 Use Case:
- Cost-aware scheduling 💰
- Latency-aware scheduling ⚡ 

---

## 10. Resource Requests & Limits (🧮 Smart Placement)
👉 Help scheduler decide placement  
```
resources:
  requests:
    cpu: "500m"
    memory: "256Mi"
```
💡 Benefit: Prevent node overload, Better utilization
---

## 11. Default Scheduler

👉 Built-in Kubernetes scheduler  

✔ Used unless custom scheduler specified  

---

## 12. Descheduler (🔄 Rebalancing)

👉 Rebalances Pods AFTER scheduling  

✔ Use case:
- Node changes  
- Cluster scaling  

---

## 13. DaemonSet (📦 One Pod Per Node)
👉 Ensures one Pod runs on every node  

💡 Use Case:
- Logging agents 📜
- Monitoring tools 📊 

---

# 🧪 Real-Life Examples

- GPU workloads → nodeSelector / taints  
- Microservices → podAffinity  
- HA systems → podAntiAffinity  
- Multi-zone apps → topologySpreadConstraints  

---

# 🔧 Important Commands
```
kubectl get nodes --show-labels                            # 🔍 Check Node Labels
kubectl label nodes <node-name> disktype=ssd               # 🏷 Add Label to Node
kubectl label nodes <node-name> zone=us-east-1a            # 📍 Assign Zone Label
kubectl taint nodes <node-name> dedicated=ml:NoSchedule    # 🚫 Taint a Node
kubectl describe node <node-name> | grep Taints            ## 🔍 Check Taints
kubectl get events --watch                                 ## 🔁 Watch Events (Real-Time)
kubectl describe pod <pod-name>                            ## 🔎 Check Pod Scheduling Info 
```

## 💡 Example: E-commerce App
| Component  | Scheduling Strategy    |
| ---------- | ---------------------- |
| Frontend   | Spread across zones    |
| Backend    | Pod affinity with DB   |
| Database   | Dedicated tainted node |
| Monitoring | DaemonSet              |

## ⚖️ Summary Table
| Feature         | Purpose               |
| --------------- | --------------------- |
| nodeSelector    | Simple node selection |
| nodeAffinity    | Advanced node rules   |
| podAffinity     | Place pods together   |
| podAntiAffinity | Spread pods apart     |
| topologySpread  | Even distribution     |
| taints          | Block nodes           |
| tolerations     | Allow specific pods   |
| priorityClass   | Pod importance        |
| preemption      | Evict lower priority pods  |
| schedulerName   | Custom scheduler      |
| descheduler     | Rebalance pods        |
| DaemonSet       | One pod per node      |

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
