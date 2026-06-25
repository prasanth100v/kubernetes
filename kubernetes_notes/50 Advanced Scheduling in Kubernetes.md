# 🚀 Advanced Scheduling in Kubernetes 
## 🌟 What is Scheduling in Kubernetes?
 * The **Kubernetes Scheduler** decides **Which node will run your Pod** using labels, rules, and constraints.
 * 👉 Main concepts:
    - NodeSelector  
    - Node Affinity  
    - Pod Affinity / Anti-Affinity  
    - Taints & Tolerations 

#### ⚙️ Default Scheduling Behavior
 * By default, Kubernetes scheduler selects nodes based on:
   - 🧮 CPU availability
   - 💾 Memory availability
   - 📦 Resource requests
   - ➡️ This is basic scheduling

## 🎯 What is Advanced Scheduling?
 * 👉 Advanced scheduling = **Control how Pods are placed on nodes**
 * Instead of letting Kubernetes decide randomly, you can:
    * 🎯 Force Pods to specific nodes
    * ⚖️ Spread Pods evenly
    * 🚫 Avoid certain nodes
    * 🧩 Place Pods close or far from each other

## 🎯 Why Do We Need Advanced Scheduling?
  * By default, Kubernetes schedules Pods based on:
      * 🧮 CPU & Memory,
      * 📦 Resource availability
  * 👉 But real-world applications need:
       - 🎯 Specific node placement
       - ⚖️ High availability
       - 🚫 Isolation of workloads
       - ⚡ Performance optimization
    * ➡️ That’s where Advanced Scheduling comes in!

---

## 🌟 Scheduling Concepts & Features
## 1. nodeSelector (🟢 Simple Scheduling)
   - 👉 Simplest way to assign Pods to nodes Using `key-value labels`
   - ✔ Pod runs ONLY on nodes matching label  

### 📌 Example Use:
```yaml
nodeSelector:
  disktype: ssd
```
### 🧠 Explanation :
   - Pod will ONLY run on nodes with: `disktype=ssd`
   - ❌ If no matching node → Pod stays Pending  

## 🎯 Use Case
  * Run only on 🎮 `GPU nodes` and 💽 `SSD storage nodes`
  * ⚠️ Important Notes :
     * ❌ No flexibility (only supports `exact match`)
     * ❌ No conditions (`AND/OR not supported`)
     * ❌ If no matching node → `Pod stays Pending`

---

## 2. nodeAffinity (🎯 Advanced Node Selection)
 * 👉 More powerful and flexible than nodeSelector
 * 🧩 Supports:
     - ✅ In
     - ❌ NotIn
     - 🔍 Exists
  * ✔ Supports:
       - 🎯 Hard rules (required)  
       - ⚖️ Soft rules (preferred) 

###🔥 Types:
| 🔢 **Type**                                       | ⚙️ **Behavior**                 | 📖 **Explanation**                                                                             |
| ------------------------------------------------- | ------------------------------- | ---------------------------------------------------------------------------------------------- |
| `requiredDuringSchedulingIgnoredDuringExecution`  | 👉 **Strict Rule (MUST match)** | ❌ If no matching node is found → Pod stays in **Pending** state                                |
| `preferredDuringSchedulingIgnoredDuringExecution` | 👉 **Soft Rule (Try to match)** | 👉 Scheduler **prefers** matching nodes <br> 👉 But Pod can still run on other nodes if needed |

## 📌 Example
```yaml
spec:
  containers:
  - name: nginx
    image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
            - nvme
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: zone
            operator: In
            values:
            - us-west-1a
```
## 🧠 Explanation
   - MUST run on `SSD` or `NVMe nodes ` 
   - Prefers zone `us-west-1a ` 
   - weight (`1–100`) → importance Higher (`more preference`)

---

## ❌ Anti-Affinity (Node Level)
  * 👉 Prevent scheduling on certain nodes:
```yaml
Use:
operator: NotIn  
```
#### 🎯 Use Case
   - Prefer specific zones 🌍
   - Avoid expensive nodes 💰

## 🎮 GPU Scheduling in Kubernetes
 ### 🔹 Using nodeSelector
```yaml
spec:
  nodeSelector:
    gpu: "true"
```

### 🔹 Using nodeAffinity
```yaml
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: gpu
            operator: In
            values:
            - "true"
```
#### 🚀 When to Use What?

| 🔢 **Method**  | 🎯 **Use Case**      | 📖 **Explanation**                                                                                                          |
| -------------- | -------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `nodeSelector` | Simple GPU selection | 👉 Best for **basic matching** using key-value labels <br> 👉 Works well for straightforward cases like selecting GPU nodes |
| `nodeAffinity` | Flexible rules       | 👉 Supports **advanced scheduling rules** (soft & hard constraints) <br> 👉 Useful for complex placement strategies         |

---

## 3. Pod Affinity (🤝 Stay Together)
 * 👉 Schedule Pods **close to other Pods**
 * Used for:
    * `Frontend + Backend` communication
    * Reduce `Low latency` communication ⚡

```yaml
    spec:
      affinity:
        podAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchLabels:
                app: cache  # Must be near cache pods
            topologyKey: kubernetes.io/hostname  # Same node
```
### 📌 Example:
   - Frontend + Backend in `same node/zone`  

### 📝 Important Clarifications
```yaml
topologyKey: kubernetes.io/hostname                # Same Node (most strict)
topologyKey: topology.kubernetes.io/zone           # Same Zone (less strict, better for availability)
topologyKey: topology.kubernetes.io/region         # Same Region (most flexible)
```
### 💡 Better Approach for Your Example:
```yaml
spec:
  affinity:
    # Use preferred instead of required if cache might not exist
    podAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchLabels:
              app: cache
          topologyKey: kubernetes.io/hostname
```
  * 🎯 This way, pods still schedule but prefer being near cache when available.

---

## 4. Pod Anti-Affinity (🚫 Stay Apart)
 * 👉 Schedule Pods **away from each other**
 * 🎯 Benefit
      * Spread replicas across nodes → `High Availability`
      * Avoid single `node failure`

### 🧾 Example (Pod Anti-Affinity)
```yaml
spec:
  containers:
  - name: nginx
    image: nginx

  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - myapp
        topologyKey: "kubernetes.io/hostname"
```
### Explanation
   - Pods with label app=myapp  
   - ❌ Will NOT run on same node  
 
 ## topologyKey (🌍 Grouping Logic)
  * 👉 Defines grouping for scheduling
  * 📌 Common Keys:
      * `kubernetes.io/hostname` → Node level       (Single node)
      * `topology.kubernetes.io/zone` → Zone level  (Entire zone)

🌐 AWS EKS Cloud Provider-Specific Topology Labels:
```yaml
topologyKeys:
 - topology.kubernetes.io/zone             # us-east-1a, us-east-1b
 - topology.kubernetes.io/region           # us-east-1
 - node.kubernetes.io/instance-type        # t3.medium, m5.large
```

### 💡Best Practices
   * For High Availability: Use `topology.kubernetes.io/zone` for `anti-affinity`
   * For Low Latency: Use `kubernetes.io/hostname` for `affinity`
   * Combine Both: Use `zone-level` for `HA`, node-level for `performance`

#### Check Available Labels: 
```yaml
kubectl describe node <node-name> | grep topology
kubectl get nodes --show-labels
```
---

## 5. Taints & Tolerations (🚫 + 🔓 Control Access)
#### 🚫 Taints (Node Protection) (Block Nodes)
  * 👉 Prevent Pods from scheduling on a node
### 📌 Example:
```yaml
# Three types of effects
kubectl taint nodes node1 key=value:NoSchedule                # Hard - won't schedule
kubectl taint nodes node1 key=value:PreferNoSchedule          # Soft - tries to avoid
kubectl taint nodes node1 key=value:NoExecute                 # Evict existing pods
```
   * NoSchedule         :	New pods won't be scheduled unless they `tolerate`
   * PreferNoSchedule	:  Scheduler tries to avoid, but not guaranteed
   * NoExecute          : 	New pods won't schedule + existing pods without toleration are evicted
```hcl
kubectl taint nodes node1 app=database:NoSchedule       # Hard - won't schedule
```
## 🧠 Meaning
  - No pod will run on node1  
  - Unless it tolerates this taint

### Common Use Cases
| 🎯 Use Case             | 🚫 Taint (on Node)                  | 🤝 Toleration (on Pod)           | 🧠 How It Works                        | 💡 Real-World Insight                             |
| ----------------------- | ----------------------------------- | -------------------------------- | -------------------------------------- | --------------------------------------------------- |
| 🎮 GPU Nodes            | `nvidia.com/gpu=true:NoSchedule`    | ML/AI pods tolerate this taint  | 👉 Only GPU workloads get scheduled    | 💰 Prevents normal apps from using costly GPU nodes |
| 🏗 Infrastructure Nodes | `node-type=infra:NoSchedule`        | Monitoring/Ingress, logging Pods tolerate | 👉 Reserve nodes for system components | 🛡️ Keeps infra isolated                    |
| ⚡ SSD Nodes             | `disk=ssd:NoSchedule`               | Database workloads tolerate     | 👉 Only high-performance apps scheduled | 🚀 Ensures fast storage usage                    |
| 🛠 Maintenance          | `maintenance=in-progress:NoExecute` | ❌ No toleration                  | 👉 All Pods are evicted from node          | 🔧 Used during upgrades or fixes              |
| 🏢 Dedicated Tenants    | `tenant=team-a:NoSchedule`          | Team-specific Pods tolerate      | 👉 Isolation per team                  | 🏢 Multi-tenant cluster  management                |


  * NoSchedule = prevent scheduling, NoExecute = evict existing pods
  * Combine with `nodeSelector` or `nodeAffinity` for precise placement
  * Use operator: "Exists" to tolerate any value for a key

## 🔥 Taint Effects
| ⚙️ **Effect**           | 📖 **Behavior**        | 🧠 **How It Works**                                                                         |
| ----------------------- | ---------------------- | --------------------------------------------------------------------------------------------- | 
| 🚫 **NoSchedule**       | ❌ Block scheduling     | 👉 New Pods **will NOT be scheduled** on the node unless they have a matching toleration    | 
| ⚠️ **PreferNoSchedule** | ⚠️ Try to avoid        | 👉 Scheduler **tries to avoid** placing Pods, but may still schedule if no other option      | 
| 🧹 **NoExecute**        | 🚫 Remove running Pods | 👉 Existing Pods **are evicted** if they don’t tolerate the taint<br>👉 Also blocks new Pods | 

## 🎯 Use Cases
   - GPU nodes  
   - Database nodes  
   - Critical workloads  
   - Infrastructure nodes

## 🔓 Tolerations (Pod Permission)
 * 👉 Allow Pods to run on tainted nodes
```yaml
spec:
  containers:
  - name: nginx
    image: nginx
  tolerations:
  - key: "app"
    operator: "Equal"
    value: "database"
    effect: "NoSchedule"
```

## 🧠 Explanation
  * Pod can run on node with taint :
  * `app=database:NoSchedule  `

### 🎯 Real-World Scenario
🛒 E-Commerce System
| 🧩 **Component**    | ⚙️ **Strategy**           | 🧠 **How It Works**                                      | 💡 **Why This Matters**                         |
| ------------------- | ------------------------- | -------------------------------------------------------- | ----------------------------------------------- |
| 🌐 **Frontend**     | 🔀 Anti-Affinity          | 👉 Pods are spread across nodes (not placed together)    | ✅ High availability, avoids single-node failure |
| ⚙️ **Backend**      | 🔗 Pod Affinity (with DB) | 👉 Backend Pods scheduled close to database Pods         | ⚡ Low latency communication                     |
| 🗄 **Database**     | 🚫 Tainted Nodes          | 👉 Only DB Pods (with toleration) can run on these nodes | 🔒 Dedicated, high-performance isolation        |
| ⚡ **Cache (Redis)** | 📍 Node Affinity (SSD)    | 👉 Pods scheduled on nodes with SSD disks                | 🚀 Faster read/write performance                |
