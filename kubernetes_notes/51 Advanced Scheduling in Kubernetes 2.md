## 6. 🎯 Topology Spread Constraints in Kubernetes (High Availability & Fault Tolerance) (⚖️ Even Distribution)
#### 🌐 What are Topology Spread Constraints?

 * 👉 Topology Spread Constraints are used to:
    * ⚖️ Evenly distribute Pods
    * 🌍 Pods are evenly distribute Across `nodes`, `zones`, or `regions`
    * 🚀 Improve `high availability` & `fault tolerance`

# 📦 Example: Topology Spread Constraint
```yaml
topologySpreadConstraints:
- maxSkew: 1
  topologyKey: "topology.kubernetes.io/zone"
  whenUnsatisfiable: DoNotSchedule
  labelSelector:
    matchLabels:
      app: nginx
```

## 🧠 Explanation
  * Spread Pods `across zones`
  * Only applies to Pods with label:
    * `app=nginx ` 
  * Ensures balanced distribution  

### topologyKey (🌍 Where to Spread)
| 🌐 **topologyKey**                   | 📖 **Meaning**          | 🧠 **How It Works**                                  | 💡 **Real-World Use Case**                       |
| ------------------------------------ | ----------------------- | ---------------------------------------------------- | ------------------------------------------------ |
| 🖥 **kubernetes.io/hostname**        | Node level              | 👉 Spreads or groups Pods across **different nodes** | 🔀 Avoid single node failure (high availability) |
| 🌍 **topology.kubernetes.io/zone**   | Availability Zone level | 👉 Distributes Pods across AZs                       | 🛡 Protect against AZ failure (cloud resilience) |
| 🌎 **topology.kubernetes.io/region** | Region level            | 👉 Spreads Pods across regions                       | 🌐 Multi-region disaster recovery                |


### 🔹 whenUnsatisfiable
  * 👉 What to do if constraint cannot be met
      * DoNotSchedule : ❌ Pod will NOT be scheduled
      * ScheduleAnyway : ✔ Pod will be scheduled anyway 

### 👉 Instead of putting all Pods in one place:
  * ❌ Bad:
     - Zone A → 5 Pods  
     - Zone B → 1 Pod
  * ✅ Good:
     - Zone A → 2 Pods  
     - Zone B → 2 Pods  
     - Zone C → 2 Pods

### 💡 Benefit:
   * Prevent all pods in one zone ❌
   * Improves HA ✅

### 🧾👉 `maxSkew` controls how strictly Kubernetes balances Pods across nodes.
#### 📊 maxSkew Values & Meaning
| 🔢 **maxSkew Value** | 📖 **Distribution Behavior** | 🔒 **Strictness Level** | 💡 **Common Usage**                        |
| -------------------- | ---------------------------- | ----------------------- | ------------------------------------------ |
| 1️⃣ **1**            | Almost equal distribution    | 🔥 Very High            | High availability apps (critical services) |
| 2️⃣ **2**            | Small imbalance allowed      | 🔒 High                 | General workloads                          |
| 3️⃣ **3**            | Moderate imbalance           | ⚖️ Medium               | Flexible scheduling                        |
| 4️⃣–🔟 **4–10**      | Loose distribution           | ⚠️ Low                  | Less critical workloads                    |
| 🔓 **>10**           | Very relaxed (almost no restriction)| ❌ Very Low      | Rarely used                                |

#### 📊 maxSkew in Topology Spread Constraints
| 📦 **Scenario**   | 🖥 **Node A** | 🖥 **Node B** | 🖥 **Node C** | 📊 **Skew (Max Difference)** | ✅ **Allowed?** |
| ----------------- | ------------- | ------------- | ------------- | ---------------------------- | -------------- |
| 3 Pods            | 1             | 1             | 1             | 0                            | ✅ Yes          |
| 4 Pods            | 2             | 1             | 1             | 1                            | ✅ Yes          |
| 5 Pods            | 2             | 2             | 1             | 1                            | ✅ Yes          |
| 6 Pods            | 2             | 2             | 2             | 0                            | ✅ Yes          |
| 4 Pods (bad case) | 3             | 1             | 0             | 3                            | ❌ No           |

```hcl
kubectl get pods -o wide | grep myapp                    # Check Pod distribution across nodes
kubectl get nodes -L topology.kubernetes.io/zone           # Check Pod distribution across zones
```

# 🧠 Quick Revision
| 🧩 Field                 | 💡 Meaning                                             |
| ------------------------ | ------------------------------------------------------ |
| 🗺️ **topologyKey**      | 📍 Defines **where to spread** Pods (e.g., zone, node) |
| ⚖️ **maxSkew**           | 📊 Allowed imbalance between groups                    |
| 🚦 **whenUnsatisfiable** | ❓ What to do if constraint can’t be met                |
| 🚫 **DoNotSchedule**     | 🔒 Strict → Pod will NOT be scheduled                  |
| 🔄 **ScheduleAnyway**    | ⚡ Flexible → Pod will be scheduled anyway              |

### 🎯 Topology spread constraints ensure Pods are evenly distributed `across nodes`, `zones`, or `regions` to improve availability and fault tolerance.

--- 

## 7. Priority Classes & Preemption (🔥 Critical Pods First)
### 🏆 Priority Class

 * 👉 A PriorityClass defines the **importance of Pods**
 * Higher priority → Scheduled first  ( `Higher value`)

#### 🧾 Define a PriorityClass
```yaml
apiVersion: scheduling.k8s.io/v1  
kind: PriorityClass  
metadata:  
  name: high-priority  
value: 1000  
globalDefault: false  
description: "High priority pods"  
```

#### 🧠 Explanation
| 🧩 Field             | 💡 Meaning                                      |
| -------------------- | ----------------------------------------------- |
| 🏷️ **name**         | 📦 Referenced in Pod spec (`priorityClassName`) |
| 🔢 **value**         | 🎯 Priority number (higher = more important)    |
| 🌍 **globalDefault** | ⚙️ Applied to Pods by default (if true)         |
| 📝 **description**   | ℹ️ Explains purpose/usage of the priority       |

### 📦 Use in Pod
```yaml
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

#### 🔥 What Happens?

 * 👉 If cluster is full:
   - Low-priority Pods → removed ( `Evicted Pods → go to Pending` )
   - High-priority Pod → scheduled
   - ⚠️ Important Notes :
       * Preemption only happens if needed
       * Only `lower-priority` Pods are evicted
       * Critical system Pods usually have `highest priority`

### ⚡ Preemption Flow (Step-by-Step)
```hcl
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
  * 👉 Preemption allows **high-priority Pods** to:
     * ❌ Evict (`remove`) lower-priority Pods  (📦 Take their resources)
     * 🏆 Schedule `higher-priority Pods`

### 🎯 Why Preemption?
 * 💡 Use Case:
    - Ensure **critical workloads always run**
    - Handle **resource shortages**
    - Maintain **service availability**  

### In real-world systems:
  - 🚀 Production apps must always run
  - 🧪 `Test/dev` apps can be sacrificed
  - ➡️ Preemption ensures: ✔️ Critical workloads always `get resources`  ✔️ Cluster is used `efficiently`
  
### 🧠 How it Works
 1. Cluster is full 🚫
 2. High-priority Pod arrives 📦  
 3. Scheduler finds no space  
 4. Lower-priority Pods are evicted ❌  
 5. High-priority Pod gets scheduled ✅ 

---

## 8. Resource Requests & Limits (🧮 Smart Placement)
  * Scheduler uses resource requests to decide : 👉 `“Can this Pod fit on this node?”`

### 🌟 What are Requests?
  * 👉 Minimum resources required for scheduling  

### 🌟 What are Limits?
  * 👉 Maximum resources container can use  

### 👉 Help scheduler decide placement  
```yaml
resources:
  requests:
    cpu: "500m"
    memory: "256Mi"
  limits:
    cpu: "1"
    memory: "512Mi"
```
 * 💡 Benefit: Prevent node overload, Better utilization

## 🧠 Explanation
  - CPU: `500m → 0.5 CPU`  
  - Memory: `256Mi`
  - 👉 Node must have at least this much free
  - Pod cannot exceed: `1 CPU`, `512Mi memory`

### ⚠️ If Limit Exceeded
| 🧩 Resource   | 💡 Behavior                                         |
| ------------- | --------------------------------------------------- |
| 🧠 **CPU**    | 🐢 Throttled (limited when exceeding request/limit) |
| 💾 **Memory** | 💥 Container killed (OOMKilled if limit exceeded)   |

 * 👉 Scheduler will: Find node with enough free resources  

## ⚠️ Why Important?
   - Without requests : Scheduler may overload node
   - With limits : Prevents resource abuse (`overuse`)
   - With Both : ✅ `Balanced scheduling` ✅ `Stable performance`

## 🎯⚙️ Resources
| 🧩 Concept      | 💡 Meaning                                                 |
| --------------- | ---------------------------------------------------------- |
| 📦 **Requests** | 🗓️ Used for **scheduling** (minimum guaranteed resources) |
| 🚫 **Limits**   | ⚙️ Used for **runtime control** (maximum allowed usage)    |

###⚠️ Best Practices
  - ✅ Use PriorityClasses for critical apps  
  - ✅ Avoid unnecessary high priorities  
  - ✅ Define proper resource requests  
  - ✅ Use limits to prevent overuse 

