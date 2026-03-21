## 6. Topology Spread Constraints (⚖️ Even Distribution)

👉 Ensures Pods are evenly distributed  , Spread replicas across zones 

✔ Prevents:
- All Pods in one zone  

### 💡 Benefit:
- Prevent all pods in one zone ❌
- Improves HA ✅

--- 

## 7. Priority Classes & Preemption (🔥 Critical Pods First)
### 🏆 Priority Class

- 👉 A PriorityClass defines the **importance of Pods**
- ✔ Higher priority → Scheduled first  ( Higher value)

#### 🧾 Define a PriorityClass
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

#### 🧠 Explanation
| Field         | Meaning                                   |
| ------------- | ----------------------------------------- |
| name          | Used in Pod spec                          |
| value         | Priority number (higher = more important) |
| globalDefault | Whether applied to all pods by default    |
| description   | Info about usage                          |

---

### 📦 Use in Pod
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

#### 🔥 What Happens?

👉 If cluster is full:
- Low-priority Pods → removed ( Evicted Pods → go to Pending )
- High-priority Pod → scheduled  

---

#### ⚠️ Important Notes

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

---

## 8. Resource Requests & Limits (🧮 Smart Placement)
Scheduler uses resource requests to decide : 👉 “Can this Pod fit on this node?”

### 🌟 What are Requests?
👉 Minimum resources required for scheduling  

### 🌟 What are Limits?
👉 Maximum resources container can use  

### 👉 Help scheduler decide placement  
```
resources:
  requests:
    cpu: "500m"
    memory: "256Mi"
  limits:
    cpu: "1"
    memory: "512Mi"
```
💡 Benefit: Prevent node overload, Better utilization

## 🧠 Explanation
- CPU: 500m → 0.5 CPU  
- Memory: 256Mi
- 👉 Node must have at least this much free
- Pod cannot exceed: 1 CPU, 512Mi memory

### ⚠️ If Limit Exceeded
| Resource | Behavior                  |
| -------- | ------------------------- |
| CPU      | Throttled 🐢              |
| Memory   | Container killed (OOM) 💥 |

👉 Scheduler will:
✔ Find node with enough free resources  

## ⚠️ Why Important?
- Without requests : Scheduler may overload node
- With limits : Prevents resource abuse (overuse)
- With Both : ✅ Balanced scheduling ✅ Stable performance

## 🎯⚙️ Resources
- 📦 Requests → Scheduling
- 🚫 Limits → Runtime control

###⚠️ Best Practices
- ✅ Use PriorityClasses for critical apps  
- ✅ Avoid unnecessary high priorities  
- ✅ Define proper resource requests  
- ✅ Use limits to prevent overuse 


---
