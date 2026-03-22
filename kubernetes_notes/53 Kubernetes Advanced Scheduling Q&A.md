# 🚀 Kubernetes Advanced Scheduling (Interview Q&A)
👉 Advanced scheduling ensures:

- ⚡ Optimal resource utilization  
- 🌍 High availability  
- 🚀 Better workload performance  

## 🌟 Hard vs Soft Affinity Rules
| Type                                                       | Rule          |      Behavior                           |
| ---------------------------------------------------------- | ------------- | ----------------------------------------- |
| 🔒 Hard (requiredDuringSchedulingIgnoredDuringExecution.)  | MUST follow   | ❌ If not satisfied → Pod stays Pending |
| 🤏 Soft (preferredDuringSchedulingIgnoredDuringExecution.) | TRY to follow | Pod still schedules if rule fails  |

### 📌 Summary
- 🔒 Hard = Strict requirement
- 🤏 Soft = Preference only

---

# 🔥 Types of Taints in Kubernetes

## 🔹 1. NoSchedule 🔒 : 👉 Pod will NOT be scheduled  ✔ Strict rule ( unless tolerated )
## 🔹 2. PreferNoSchedule 🤏 : 👉 Avoid scheduling  ✔ But may still schedule  
## 🔹 3. NoExecute 🚫 : 👉 Special behavior: ❌ New Pods → NOT scheduled and 💥 Existing Pods → Evicted 🎯 Use Case - Node maintenance and Node unhealthy  

---

# ❓ Common Scenarios & Answers
##  What if ALL nodes are tainted (NoSchedule) and no toleration❓

👉 🚫 No Pods will be scheduled

✔ Fix:
- Add toleration  
- OR remove taint  

### How to restrict GPU nodes❓
🎮 Example: GPU Node Isolation
```
kubectl taint nodes gpu-node type=gpu:NoSchedule
```
👉 Prevents normal Pods from using GPU nodes

---

## DaemonSet + Taints (📦 Special Case)
#### How to run DaemonSet on tainted nodes ❓

👉 Add matching tolerations in DaemonSet spec
```
tolerations:
- operator: "Exists"
```

#### 💡 Why?
  Ensures logging/monitoring agents run everywhere

---

## nodeSelector + nodeAffinity Together
#### What happens if both are defined❓

👉 Both conditions must be satisfied ✅, ⚠️ If any fails : ❌ Pod remains Pending

---

# 🧠 Real-World Scheduling Scenarios

---

## 🎯 Scenario 1: Stateful app on SSD + specific zone

👉 Requirement:
- SSD nodes  
- Zone us-east-1a 🌍

### ✅ Solution
```
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
  👉 Pod runs ONLY if both conditions match
---

## 🎯 Scenario 2: 5 replicas, avoid same node

👉 Requirement: Spread Pods across nodes  

### ✅ Solution
```
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
---

## 🎯 Scenario 3: Allow Pods on tainted nodes

👉 Requirement:
- Node taint:
  dedicated=high-perf:NoSchedule  

### ✅ Solution
```
tolerations:
- key: "dedicated"
  operator: "Equal"
  value: "high-perf"
  effect: "NoSchedule"
```
👉 Pod can run on restricted nodes
---

## 🎯 Scenario 4: Critical workload (Preemption)

✔ High priority pods → Evicts low priority Pods  

---

## 🎯 Scenario 5: Pod stuck in Pending

👉 Debug steps:
```
 kubectl describe pod <pod-name>     #✔ Check events    
 kubectl get nodes --show-labels     # Check node labels
 kubectl describe node <node-name>    #Check taints 
 Validate:
   - nodeAffinity 🎯
   - tolerations 🔓
   - resources 📦
```
---

## 🎯 Scenario 6: Prefer SSD but allow others

👉 Requirement:
- Prefer SSD  
- Allow other nodes if needed

### ✅ Solution
```
affinity:
  nodeAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:          #👉 Runs on SSD if available 👉 Otherwise runs anywhere
    - weight: 1
      preference:
        matchExpressions:
        - key: disktype
          operator: In
          values:
          - ssd
```
---

## 🎯 Scenario 7: Drain Node (Stop New Pods, Keep Existing)

👉 Requirement:
- Do NOT schedule new Pods  
- Existing Pods should continue running  

### ✅ Solution
```
kubectl taint nodes <node-name> key=value:NoSchedule
```
### 🧠 What Happens?
- ❌ New Pods → NOT scheduled  
- ✔ Existing Pods → Continue running  

### 🎯 Use Case

- Node maintenance  
- Gradual migration  
- Safe draining  

---

# 🔥 Scenario 8: High Priority Pod in Full Cluster

👉 Problem:
- Cluster is full  
- Low-priority Pods running  
- High-priority Pod waiting  

### ✅ Solution

1. Create PriorityClass  
2. Assign to Pod  

### 🧠 What Kubernetes Does

- Detects high-priority Pod  
- Evicts low-priority Pods  
- Frees resources  
- Schedules high-priority Pod  

### ⚡ This is called: **Preemption**

---

# 🌍 Scenario 9: Topology Spread + Zone Failure

👉 Requirement:
- Spread Pods across zones  
- One zone becomes unavailable  

## 🔹 Option 1: Strict Mode
```
whenUnsatisfiable: DoNotSchedule
```

### 🧠 Behavior
- ❌ Pod NOT scheduled  
- Maintains strict distribution  

## 🔹 Option 2: Flexible Mode
```
whenUnsatisfiable: ScheduleAnyway
```
### 🧠 Behavior

- ✔ Pod scheduled anyway  
- Distribution rule ignored  

### 🎯 Use Case
| Mode              | When to Use                     |
|------------------|--------------------------------|
| DoNotSchedule    | Strict HA requirement          |
| ScheduleAnyway   | Availability over strict rules |


---
### 🎯 Key Concepts
- 🔒 Hard rules → Strict scheduling
- 🤏 Soft rules → Flexible scheduling
- 🚫 Taints → Block nodes
- 🔓 Tolerations → Allow pods
- 🏆 Priority → Decide importance

# 🧠 Quick Revision

- Hard affinity → must match  
- Soft affinity → try match  
- NoSchedule → strict block  
- PreferNoSchedule → soft avoid  
- NoExecute → evict  
- podAntiAffinity → spread  
- nodeAffinity → control placement  

---

# 🎯 One-Line Answer

Advanced scheduling in Kubernetes uses affinity rules, taints, tolerations, and priorities to precisely control Pod placement for performance, availability, and resource optimization.
