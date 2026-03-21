## 6. Topology Spread Constraints (⚖️ Even Distribution)

👉 Ensures Pods are evenly distributed  , Spread replicas across zones 

✔ Prevents:
- All Pods in one zone  

### 💡 Benefit:
- Prevent all pods in one zone ❌
- Improves HA ✅

--- 

## 7. Priority Classes & Preemption (🔥 Critical Pods First)
#### 🏆 Priority Class

- 👉 A PriorityClass defines the **importance of Pods**
- ✔ Higher priority → Scheduled first  ( Higher value)

## 🧾 Define a PriorityClass
```
apiVersion: scheduling.k8s.io/v1  
kind: PriorityClass  
metadata:  
  name: high-priority  
value: 1000  
globalDefault: false  
description: "High priority pods"  
```
---

## 🧠 Explanation
| Field         | Meaning                                   |
| ------------- | ----------------------------------------- |
| name          | Used in Pod spec                          |
| value         | Priority number (higher = more important) |
| globalDefault | Whether applied to all pods by default    |
| description   | Info about usage                          |

---

## 📦 Use in Pod
```
apiVersion: v1  
kind: Pod  
metadata:  
  name: high-priority-pod  
spec:  
  priorityClassName: high-priority  
  containers:  
  - name: nginx  
    image: nginx  
```
---

## 🔥 What Happens?

👉 If cluster is full:
- Low-priority Pods → removed ( Evicted Pods → go to Pending )
- High-priority Pod → scheduled  

---

## ⚠️ Important Notes

- Preemption only happens if needed  
- Only lower-priority Pods are evicted  
- Critical system Pods usually have highest priority

### ⚡ Preemption Flow (Step-by-Step)
```
Cluster Full 🚫
      ↓
High Priority Pod Arrives 🏆
      ↓
Find Lower Priority Pods 🔍
      ↓
Evict Them ❌
      ↓
Schedule High Priority Pod ✅
```

---

## ⚡ Preemption
### What is Pod Preemption?
👉 Preemption allows **high-priority Pods** to:

- ❌ Evict (remove) lower-priority Pods  (📦 Take their resources)
- 🏆 Schedule higher-priority Pods

### 🎯 Why Preemption?
💡 Use Case:
- Ensure **critical workloads always run**  
- Handle **resource shortages**  
- Maintain **service availability**  

### In real-world systems:
- 🚀 Production apps must always run
- 🧪 Test/dev apps can be sacrificed

  ➡️ Preemption ensures: ✔️ Critical workloads always get resources  ✔️ Cluster is used efficiently
  
### 🧠 How it Works

1. Cluster is full 🚫
2. High-priority Pod arrives 📦  
3. Scheduler finds no space  
4. Lower-priority Pods are evicted ❌  
5. High-priority Pod gets scheduled ✅  


## 8. schedulerName (🧠 Custom Scheduler)

👉 Use custom scheduler instead of default  
```
schedulerName: my-custom-scheduler
```
💡 Use Case:
- Cost-aware scheduling 💰
- Latency-aware scheduling ⚡ 

---

## 9. Resource Requests & Limits (🧮 Smart Placement)
👉 Help scheduler decide placement  
```
resources:
  requests:
    cpu: "500m"
    memory: "256Mi"
```
💡 Benefit: Prevent node overload, Better utilization
---

## 10. Default Scheduler

👉 Built-in Kubernetes scheduler  

✔ Used unless custom scheduler specified  

---

## 11. Descheduler (🔄 Rebalancing)

👉 Rebalances Pods AFTER scheduling  

✔ Use case:
- Node changes  
- Cluster scaling  

---

## 12. DaemonSet (📦 One Pod Per Node)
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
