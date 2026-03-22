## 6. 🎯 Topology Spread Constraints in Kubernetes (High Availability & Fault Tolerance) (⚖️ Even Distribution)
#### 🌐 What are Topology Spread Constraints?

👉 Topology Spread Constraints are used to:

- ⚖️ Evenly distribute Pods
- 🌍 Pods are evenly distribute Across nodes, zones, or regions
- 🚀 Improve high availability & fault tolerance

# 📦 Example: Topology Spread Constraint
```
topologySpreadConstraints:
- maxSkew: 1
  topologyKey: "topology.kubernetes.io/zone"
  whenUnsatisfiable: DoNotSchedule
  labelSelector:
    matchLabels:
      app: nginx
```

## 🧠 Explanation

- Spread Pods across zones  
- Only applies to Pods with label:
  app=nginx  
- Ensures balanced distribution  

### topologyKey (🌍 Where to Spread)
| topologyKey                   | Meaning      |
| ----------------------------- | ------------ |
| kubernetes.io/hostname        | Node level   → Avoid single node failure  |
| topology.kubernetes.io/zone   | Zone level   → Handle AZ failures  |
| topology.kubernetes.io/region | Region level → Multi-region resilience |

### 🔹 whenUnsatisfiable
👉 What to do if constraint cannot be met  

- DoNotSchedule : ❌ Pod will NOT be scheduled  
- ScheduleAnyway : ✔ Pod will be scheduled anyway 

### 👉 Instead of putting all Pods in one place:
❌ Bad:
- Zone A → 5 Pods  
- Zone B → 1 Pod  

✅ Good:
- Zone A → 2 Pods  
- Zone B → 2 Pods  
- Zone C → 2 Pods  

✔ Prevents : All Pods in one zone  

### 💡 Benefit:
- Prevent all pods in one zone ❌
- Improves HA ✅

### 🧾👉 maxSkew controls how strictly Kubernetes balances Pods across nodes.
#### 📊 maxSkew Values & Meaning
| **maxSkew Value** | **Distribution Behavior**            | **Strictness Level** | **Common Usage**        |
| ----------------- | ------------------------------------ | -------------------- | ----------------------- |
| 1                 | Almost equal distribution            | Very High            | High availability apps  |
| 2                 | Small imbalance allowed              | High                 | General workloads       |
| 3                 | Moderate imbalance                   | Medium               | Flexible scheduling     |
| 4–10              | Loose distribution                   | Low                  | Less critical workloads |
| >10               | Very relaxed (almost no restriction) | Very Low             | Rarely used             |

#### 📊 maxSkew in Topology Spread Constraints
| **Scenario** | **Node A** | **Node B** | **Node C** | **Skew (Max Difference)** | **Allowed?** |
| ------------ | ---------- | ---------- | ---------- | ------------------------- | ------------ |
| 3 Pods       | 1          | 1          | 1          | 0                         | ✓            |
| 4 Pods       | 2          | 1          | 1          | 1                         | ✓            |
| 5 Pods       | 2          | 2          | 1          | 1                         | ✓            |
| 6 Pods       | 2          | 2          | 2          | 0                         | ✓            |
| 4 Pods       | 3          | 1          | 0          | 3                         | ✗            |

```
kubectl get pods -o wide | grep myapp                    # Check Pod distribution across nodes
kubectl get nodes -L topology.kubernetes.io/zone           # Check Pod distribution across zones
```

# 🧠 Quick Revision

- topologyKey → Where to spread  
- maxSkew → Balance limit  
- whenUnsatisfiable → What if not possible  
- DoNotSchedule → strict  
- ScheduleAnyway → flexible

### 🎯 Topology spread constraints ensure Pods are evenly distributed across nodes, zones, or regions to improve availability and fault tolerance.

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
