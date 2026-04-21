
## 9. schedulerName (💡 Custom Scheduler)

 * 👉 Use custom scheduler instead of default  
```yaml
schedulerName: my-custom-scheduler
```
### Simple Understanding
  - 👉 Kubernetes uses `default-scheduler` by default
  - 👉 You can override it with your own scheduler logic
  - 👉 schedulerName lets you replace Kubernetes default scheduling logic with your own decision-making system.
  - 💡Use Case: 💰 `Cost-aware scheduling`, ⚡`Latency-aware scheduling`  

## 10. Default Scheduler
##### 🎯 What is Default Scheduler?
 * 👉 Default Scheduler is the built-in Kubernetes component that decides which node a `Pod should run on`.
 * Used unless custom scheduler specified  
### 📄 YAML Example (Using Default Scheduler)
👉 You don’t need to define anything — it’s used automatically:
```yaml
apiVersion: v1
kind: Pod                                       # This Pod will be scheduled by: 👉 default-scheduler
metadata:
  name: my-app
spec:
  containers:
  - name: my-container
    image: nginx
```

---

## 11. Descheduler (🔄 Rebalancing)

 * 👉 Descheduler is a Kubernetes component that rebalances Pods after they are already running.
 * It evicts poorly placed Pods so the scheduler can place them better.
### 📄 Descheduler Policy YAML
```yaml
apiVersion: "descheduler/v1alpha2"
kind: "DeschedulerPolicy"
strategies:
  RemoveDuplicates:
    enabled: true
  LowNodeUtilization:
    enabled: true
    params:
      thresholds:
        cpu: 20
        memory: 20
        pods: 20
      targetThresholds:
        cpu: 50
        memory: 50
        pods: 50
```
#### 💡 What this does
   - RemoveDuplicates   : 👉 Removes duplicate Pods from same node
   - LowNodeUtilization : 👉 Moves Pods from `overloaded nodes → underutilized nodes`


| 🎯 Use Case            | 📖 What Happens                                   | 🧠 How Kubernetes Handles It                                                                                        | 💡 Real-World Insight                               |
| ---------------------- | -------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| 🖥 **Node changes**    | Pods move when nodes fail (unhealthy) or overload  | 👉 Scheduler reschedules Pods on healthy nodes<br>👉 Controllers (Deployment/ReplicaSet) recreate Pods if needed    | 🔁  Ensures high availability during node failures |
| 📈 **Cluster scaling** | Pods distributed when new nodes are added         | 👉 New Pods scheduled on new nodes<br>👉  Existing Pods may rebalance (with advanced configs) (affinity, spread)    | ⚡ Better resource utilization                     |


### 🎯 Real-life Analogy 
 * 👉 Think of it like seating in a classroom:
      - Teacher (`Scheduler`) assigns seats initially
      - Later, if students are unevenly distributed (`Descheduler`)
  * 👉 `“Scheduler places Pods first, Descheduler fixes them later.”`

---

## 12. DaemonSet (📦 One Pod Per Node)
 * 👉 Ensures one Pod runs on every node
 * 💡 Use Case:
     * Logging agents 📜
     * Monitoring tools 📊 

### 🧪 Real-Life Examples

| 🎯 Use Case        | 🛠 Strategy                     | 🧠 How It Works                                                                     | 💡 Why It Matters                 |
| ------------------ | ------------------------------- | ----------------------------------------------------------------------------------- | --------------------------------- |
| 🎮 GPU workloads   | 📍 `nodeSelector` / 🚫 `taints` | 👉 Schedule Pods only on GPU nodes<br>👉 Taints block others, tolerations allow GPU Pods | 💰 Prevents costly GPU misuse     |
| 🔗 Microservices   | 🤝 `podAffinity`                | 👉 Place related Pods close together (same node/zone)                             | ⚡ Reduces latency between services |
| 🛡 HA systems      | 🔀 `podAntiAffinity`            | 👉 Spread Pods across nodes                                                         | ✅ Avoid single point of failure   |
| 🌍 Multi-zone apps | ⚖️ `topologySpreadConstraints`  | 👉 Distribute Pods evenly across zones                                              | 🛡️ Protect against zone failures |

---

## 🔧 Important Commands
```hcl
kubectl get pods                              # 📦 List all pods
kubectl get pods -o wide                      # 📦 Pods with node info
kubectl get nodes                             # 🖥️ List nodes
kubectl describe node <node-name>             # 🖥️ Node details (labels, taints, capacity)

                                                            # 🏷️ Labels & Node Selection
kubectl label nodes <node-name> disktype=ssd                   # Add label to node
kubectl get nodes --show-labels                                # View node labels

                                                    #⚖️ Topology Spread
kubectl get pods -o wide                                  # Check pod distribution across nodes
kubectl get pods --show-labels                            # Check labels used in affinity

                                                                    # 🔄 Drain & Rebalance (like Descheduler)
kubectl drain <node-name> --ignore-daemonsets                             # Safely remove pods from node
kubectl uncordon <node-name>                                              # Make node schedulable again

                                                                       # ⚠️ Taints & Tolerations
kubectl taint nodes <node-name> node-type=infra:NoSchedule              # Add taint to node
kubectl taint nodes <node-name> node-type=infra:NoSchedule-             # Remove taint
kubectl describe node <node-name> | grep Taints                         # View taints
kubectl describe pod <pod-name>                                                 # Check why pod is not scheduled
kubectl get pods -w                                                             # Watch pod scheduling live


kubectl get nodes --show-labels                                              # 🔍 Check Node Labels
kubectl label nodes <node-name> disktype=ssd                                 # 🏷 Add Label to Node
kubectl label nodes <node-name> zone=us-east-1a                              # 📍 Assign Zone Label
kubectl taint nodes <node-name> dedicated=ml:NoSchedule                      # 🚫 Taint a Node
kubectl describe node <node-name> | grep Taints                              ## 🔍 Check Taints
kubectl get events --watch                                                   ## 🔁 Watch Events (Real-Time)
kubectl describe pod <pod-name>                                              ## 🔎 Check Pod Scheduling Info 
```

## 💡 Example: E-commerce App
| 🧩 Component  | 💡 Scheduling Strategy                                 |
| ------------- | ------------------------------------------------------ |
| 🌐 Frontend   | ⚖️ Spread across zones (HA, load distribution)         |
| 🔗 Backend    | 🤝 Pod affinity with DB (low latency communication)    |
| 🗄 Database   | 🚫 Dedicated tainted node (isolated, high-performance) |
| 📊 Monitoring | 📡 DaemonSet (runs on every node)                      |

## ⚖️ Summary Table

| 🧩 **Feature**         | 🎯 **Purpose**          | 🧠 **How It Works**                             | 💡 **Real-World Use Case**     |
| ---------------------- | ----------------------- | ----------------------------------------------- | --------------------------------- |
| 📍 **nodeSelector**    | Simple node selection   | 👉 Matches node labels exactly                  | Run app on `ssd=true` nodes    |
| 🎯 **nodeAffinity**    | Advanced node rules     | 👉 Supports `In`, `NotIn`, `Exists` conditions  | Flexible node placement        |
| 🔗 **podAffinity**     | Place pods together     | 👉 Schedules Pods near each other               | Microservices communication (latency) |
| 🔀 **podAntiAffinity** | Spread pods apart       | 👉 Avoids placing Pods on same node             | High availability apps         |
| ⚖️ **topologySpread**  | Even distribution       | 👉 Balances Pods across nodes/zones             | Multi-zone deployments         |
| 🚫 **taints**          | Block nodes             | 👉 Prevents Pods from scheduling                | Reserve GPU/infra nodes        |
| 🤝 **tolerations**     | Allow specific pods     | 👉 Lets Pods bypass taints                      | Allow only DB Pods on DB nodes |
| 🔝 **priorityClass**   | Pod importance          | 👉 Assigns priority value to Pods               | Critical system Pods           |
| ⚔️ **preemption**      | Evict low-priority pods | 👉 Higher priority Pods replace lower ones      | Ensure critical workloads run  |
| ⚙️ **schedulerName**   | Custom scheduler        | 👉 Use alternative scheduler instead of default | Advanced scheduling logic      |
| 🔄 **descheduler**     | Rebalance pods          | 👉 Moves Pods to improve distribution           | Fix uneven workloads (Fix imbalance) |
| 📦 **DaemonSet**       | One Pod per node        | 👉 Automatically runs Pod on every node         | Logging, monitoring agents     |
 

### 🎯 One-Line Answer

  * Advanced scheduling in Kubernetes allows precise control over Pod placement using `affinity rules`, `taints`, `topology`, and `priority` mechanisms.
