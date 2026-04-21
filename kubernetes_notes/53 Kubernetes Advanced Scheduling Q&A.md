# 🚀 Kubernetes Advanced Scheduling (Interview Q&A)

 * 👉 Advanced scheduling ensures:
    * ⚡ Optimal resource utilization
    * 🌍 High availability
    * 🚀 Better workload performance  

## 🌟 Hard vs Soft Affinity Rules
| 🧩 **Type** | 📌 **Rule Name**                                  | 🎯 **Behavior**  | 🧠 **How It Works**                                                                   | 💡 **Real-World Use Case**                           |
| ----------- | ------------------------------------------------- | ---------------- | ------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| 🔒 **Hard** | `requiredDuringSchedulingIgnoredDuringExecution`  | ❌ MUST follow    | 👉 Scheduler enforces rule strictly<br>👉 If no matching node → Pod stays **Pending** | 🛡️ Critical workloads (compliance, dedicated nodes)     |
| 🤏 **Soft** | `preferredDuringSchedulingIgnoredDuringExecution` | ⚠️ TRY to follow | 👉 Scheduler prefers matching nodes<br>👉 If not available → schedules anyway         | 💰 Flexible workloads (cost optimization, general apps) |

### 📌 Summary
   - 🔒 Hard = Strict requirement
   - 🤏 Soft = Preference only


## 🔥 Types of Taints in Kubernetes
| ⚙️ **Effect**           | 📖 **Behavior**                               | 🧠 **How It Works**                                               | 🎯 **Use Case**                         | 💡 **Key Insight**                |
| ----------------------- | --------------------------------------------- | ----------------------------------------------------------------- | --------------------------------------- | --------------------------------- |
| 🔒 **NoSchedule**       | ❌ Pod will NOT be scheduled                   | 👉 Scheduler blocks new Pods unless they have matching toleration | Dedicated nodes (GPU, infra)            | Strict rule for placement control |
| 🤏 **PreferNoSchedule** | ⚠️ Avoid scheduling                           | 👉 Scheduler tries to avoid but may still place Pods if no choice | Soft isolation (non-critical workloads) | Best-effort rule                  |
| 🚫 **NoExecute**        | ❌ New Pods blocked + 💥 Existing Pods evicted | 👉 Pods without toleration are removed from node                  | Node maintenance, unhealthy nodes       | Strongest enforcement             |

---

# Common Scenarios & Answers
##  What if ALL nodes are tainted (NoSchedule) and no toleration❓

  * 🚫 No Pods will be scheduled
  * Fix: `Add toleration` OR `remove taint`  

### How to restrict GPU nodes❓
  * 🎮 Example: GPU Node Isolation
  * 👉 Prevents normal Pods from using GPU nodes
```hcl
kubectl taint nodes gpu-node type=gpu:NoSchedule
```

#### How to run DaemonSet on tainted nodes ❓

  * 👉 Add matching tolerations in DaemonSet spec
  * 💡 Why?
     * Ensures logging/monitoring agents run everywhere
```yaml
tolerations:
- operator: "Exists"
```

## nodeSelector + nodeAffinity Together
#### What happens if both are defined❓

 * 👉 Both conditions must be satisfied ✅, ⚠️ If any fails : ❌ Pod remains Pending

# 🧠 Real-World Scheduling Scenarios
## 🎯 Scenario 1: Stateful app on SSD + specific zone

 * 👉 Requirement:
   - SSD nodes  
   - Zone us-east-1a 🌍

### ✅ Solution
```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: disktype
          operator: In
          values:
          - ssd
        - key: topology.kubernetes.io/zone
          operator: In
          values:
          - us-east-1a
```
   * 👉 Pod runs ONLY if both conditions match


## 🎯 Scenario 2: 5 replicas, avoid same node

 * 👉 Requirement: Spread Pods across nodes  

### ✅ Solution
```yaml
podAntiAffinity:
  requiredDuringSchedulingIgnoredDuringExecution:
  - labelSelector:
      matchExpressions:
      - key: app
        operator: In
        values:
        - myapp
    topologyKey: "kubernetes.io/hostname"           # 👉 Each Pod runs on different node
```

## 🎯 Scenario 3: Allow Pods on tainted nodes

 * 👉 Requirement:
 * Node taint:
    * `dedicated=high-perf:NoSchedule  `

### ✅ Solution
```yaml
tolerations:
- key: "dedicated"
  operator: "Equal"
  value: "high-perf"
  effect: "NoSchedule"
```
 * 👉 Pod can run on restricted nodes

## 🎯 Scenario 4: Critical workload (Preemption)
 * High priority pods → Evicts low priority Pods  

## 🎯 Scenario 5: Pod stuck in Pending

👉 Debug steps:
```yaml
 kubectl describe pod <pod-name>              # Check events    
 kubectl get nodes --show-labels              # Check node labels
 kubectl describe node <node-name>            # Check taints 
 Validate:
   - nodeAffinity 🎯
   - tolerations 🔓
   - resources 📦
```

## 🎯 Scenario 6: Prefer SSD but allow others

 * 👉 Requirement:
    - Prefer SSD  
    - Allow other nodes if needed

### ✅ Solution
```yaml
affinity:
  nodeAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:                   #👉 Runs on SSD if available 👉 Otherwise runs anywhere
    - weight: 1
      preference:
        matchExpressions:
        - key: disktype
          operator: In
          values:
          - ssd
```


## 🎯 Scenario 7: Drain Node (Stop New Pods, Keep Existing)

 * 👉 Requirement:
   - Do NOT schedule new Pods  
   - Existing Pods should continue running

### ✅ Solution
```hcl
kubectl taint nodes <node-name> key=value:NoSchedule
```
### 🧠 What Happens?
  * ❌ New Pods → NOT scheduled
  * Existing Pods → Continue running  

### 🎯 Use Case
  - Node maintenance  
  - Gradual migration  
  - Safe draining


# 🔥 Scenario 8: High Priority Pod in Full Cluster

 * 👉 Problem:
    - Cluster is full  
    - Low-priority Pods running  
    - High-priority Pod waiting

  * ✅ Solution :
      * 1. Create PriorityClass
      * 2. Assign to Pod  
  * 🧠 What Kubernetes Does :
      * Detects high-priority Pod
      * Evicts low-priority Pods
      * Frees resources
      * Schedules high-priority Pod  

### ⚡ This is called: **Preemption**


## 🌍 Scenario 9: Topology Spread + Zone Failure

 * 👉 Requirement:
    - Spread Pods across zones  
    - One zone becomes unavailable  

## 🔹 Option 1: Strict Mode
```hcl
whenUnsatisfiable: DoNotSchedule
```

 * 🧠 Behavior
   - ❌ Pod NOT scheduled  
   - Maintains strict distribution  

## 🔹 Option 2: Flexible Mode
```hcl
whenUnsatisfiable: ScheduleAnyway
```
 * 🧠 Behavior
    - Pod scheduled anyway  
    - Distribution rule ignored

### 🎯 Use Case
| ⚙️ **Mode**           | 📖 **When to Use**             | 🧠 **How It Works**                                                                 | 💡 **Real-World Example**                                   |
| --------------------- | ------------------------------ | ----------------------------------------------------------------------------------- | ----------------------------------------------------------- |
| 🔒 **DoNotSchedule**  | Strict HA requirement          | 👉 If spreading rules (like `maxSkew`) are violated → Pod **will NOT be scheduled** | 🏦 Critical apps (banking/payments) needing even distribution |
| 🤏 **ScheduleAnyway** | Availability over strict rules | 👉 Scheduler **tries** to follow spread rules but still schedules if not possible   | 🌐 General apps                                              |


### 🎯 Key Concepts
| 🧩 Concept         | 💡 Meaning                                    |
| ------------------ | --------------------------------------------- |
| 🔒 **Hard rules**  | ❌ Strict scheduling (must satisfy conditions) |
| 🤏 **Soft rules**  | ⚠️ Flexible scheduling (best effort)          |
| 🚫 **Taints**      | ⛔ Block nodes from accepting Pods             |
| 🔓 **Tolerations** | ✅ Allow Pods to bypass taints                 |
| 🏆 **Priority**    | 🔝 Decide which Pods are more important       |
| 🔒 **Hard affinity**    | ❌ Must match (strict rule)               |
| 🤏 **Soft affinity**    | ⚠️ Try to match (best effort)            |
| 🚫 **NoSchedule**       | ⛔ Strict block (Pod not scheduled)       |
| ⚠️ **PreferNoSchedule** | 🚧 Soft avoid (scheduler tries to avoid) |
| 💥 **NoExecute**        | 🚪 Evicts existing Pods from node        |
| 🔀 **podAntiAffinity**  | ⚖️ Spread Pods across nodes              |
| 📍 **nodeAffinity**     | 🎯 Control where Pods are placed         |

## 🎯 One-Line Answer

  * Advanced scheduling in Kubernetes uses `affinity rules`, `taints`, `tolerations`, and priorities to precisely control Pod placement for performance, availability, and resource optimization.
