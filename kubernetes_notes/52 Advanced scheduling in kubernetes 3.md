## 9. schedulerName (💡 Custom Scheduler)
 * 👉 Use custom scheduler instead of default  
```yaml
schedulerName: my-custom-scheduler
```
### Simple Understanding
  - 👉 Kubernetes uses `default-scheduler` by default.
  - 👉 You can override it with your own scheduler logic.
  - 👉 schedulerName lets you replace Kubernetes `default scheduling logic` with your own decision-making system.
  - 💡Use Case: 💰 `Cost-aware scheduling`, ⚡`Latency-aware scheduling`.

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
 * It evicts poorly placed Pods so the scheduler can place them better to improve cluster balance..
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
| 🎯 Use Case        | 🛠 Strategy                     | 🧠 How It Works                                                                    | 💡 Why It Matters                |
| ------------------ | ------------------------------- | ----------------------------------------------------------------------------------- | --------------------------------- |
| 🎮 GPU workloads   | 📍 `nodeSelector` / 🚫 `taints` | 👉 Schedule Pods only on GPU nodes<br>👉 Taints block others, tolerations allow GPU Pods | 💰 Prevents costly GPU misuse |
| 🔗 Microservices   | 🤝 `podAffinity`                | 👉 Place related Pods close together (same node/zone)                             | ⚡ Reduces latency between services |
| 🛡 HA systems      | 🔀 `podAntiAffinity`            | 👉 Spread Pods across nodes                                                        | ✅ Avoid single point of failure   |
| 🌍 Multi-zone apps | ⚖️ `topologySpreadConstraints`  | 👉 Distribute Pods evenly across zones                                             | 🛡️ Protect against zone failures   |

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
| 🧩 Component  | 💡 Scheduling Strategy                                    |
| ------------- | ----------------------------------------------------------- |
| 🌐 Frontend   | ⚖️ Spread across zones (`HA`, `load distribution`)         |
| 🔗 Backend    | 🤝 Pod affinity with DB (`low latency communication`)     |
| 🗄 Database   | 🚫 Dedicated tainted node (`isolated`, `high-performance`) |
| 📊 Monitoring | 📡 DaemonSet (`runs on every node`)                       |

## ⚖️ Summary Table
| 🧩 **Feature**         | 🎯 **Purpose**         | 🧠 **How It Works**                             | 💡 **Real-World Use Case**     |
| ---------------------- | ----------------------- | ----------------------------------------------- | ------------------------------- |
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

---

### 🚀 Advanced Scheduling in Kubernetes – Rapid Fire Interview Questions & Answers
| 🔢  | ❓ Question                             | ✅ Answer                                                                                                                                                                                                 |
| --- | ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1️⃣ | What is Scheduling in Kubernetes?        | 📅 The process of assigning a Pod to the most suitable Node.                                                                                                                                                 |
| 2️⃣ | Which component schedules Pods?          | 🤖 **kube-scheduler**                                                                                                                                                                                        |
| 3️⃣ | When does scheduling happen?             | 🚀 After a Pod is created and before it starts running.                                                                                                                                                      |
| 4️⃣ | What is Advanced Scheduling?             | 🎯 Techniques to control where Pods run using rules and priorities.                                                                                                                                          |
| 5️⃣ | Why use Advanced Scheduling?             | 🛡️ High Availability, Performance, Cost Optimization, Compliance.                                                                                                                                           |
| 6️⃣ | What scheduling features are available?  | 🏷️ NodeSelector, 🧩 Node Affinity, 🚫 Taints & Tolerations, 🤝 Pod Affinity, 🚫 Pod Anti-Affinity, 🌍 Topology Spread Constraints, ⭐ Priority Classes, 🔄 Preemption, 🎛️ Custom Scheduler, 🧹 Descheduler. |
| 7️⃣ | Default scheduler name?                  | `default-scheduler`                                                                                                                                                                                          |
| 8️⃣ | Can Kubernetes have multiple schedulers? | ✅ Yes                                                                                                                                                                                                        |
| 9️⃣ | Where is the scheduler configured?       | 📄 `schedulerName` field in Pod spec.                                                                                                                                                                        |
| 🔟  | Command to check scheduler Pods?         | `kubectl get pods -n kube-system`                                                                                                                                                                            |
| 1️⃣1️⃣ | What is NodeSelector?                   | 🏷️ Simplest way to schedule Pods on labeled Nodes. |
| 1️⃣2️⃣ | Requirement for NodeSelector?           | 🏷️ Matching Node labels.                           |
| 1️⃣3️⃣ | Can NodeSelector use expressions?       | ❌ No                                                |
| 1️⃣4️⃣ | Is NodeSelector mandatory or preferred? | ✅ Mandatory                                         |
| 1️⃣5️⃣ | Command to label a Node?                | `kubectl label nodes worker1 disktype=ssd`          |
| 1️⃣6️⃣ | What is Node Affinity?             | 🧩 Advanced version of NodeSelector.                                                                        |
| 1️⃣7️⃣ | Two types of Node Affinity?        | ✅ `requiredDuringSchedulingIgnoredDuringExecution` and 💡 `preferredDuringSchedulingIgnoredDuringExecution` |
| 1️⃣8️⃣ | Which type is mandatory?           | `requiredDuringSchedulingIgnoredDuringExecution`                                                            |
| 1️⃣9️⃣ | Which type is optional?            | `preferredDuringSchedulingIgnoredDuringExecution`                                                           |
| 2️⃣0️⃣ | Can Node Affinity use expressions? | ✅ Yes (`In`, `NotIn`, `Exists`, `DoesNotExist`, `Gt`, `Lt`)                                                 |
| 2️⃣1️⃣ | What is a Taint?                                    | 🚫 Prevents Pods from being scheduled on a Node.                            |
| 2️⃣2️⃣ | What is a Toleration?                               | ✅ Allows a Pod to be scheduled on a `tainted Node`.                           |
| 2️⃣3️⃣ | Command to taint a Node?                            | `kubectl taint nodes worker1 key=value:NoSchedule`                          |
| 2️⃣4️⃣ | Effect `NoSchedule`?                                | 🚫 New Pods won't be scheduled.                                             |
| 2️⃣5️⃣ | Effect `PreferNoSchedule`?                          | ⚠️ Avoid scheduling if possible.                                            |
| 2️⃣6️⃣ | Effect `NoExecute`?                                 | 🚪 Prevents new Pods and evicts existing Pods without matching tolerations. |
| 2️⃣7️⃣ | Can a Pod run on a tainted Node without toleration? | ❌ No                                                                        |
| 2️⃣8️⃣ | Remove a taint?                                     | `kubectl taint nodes worker1 key=value:NoSchedule-`                         |
| 2️⃣9️⃣ | What is Pod Affinity?      | 🤝 Schedule Pods close to specific Pods.             |
| 3️⃣0️⃣ | What is Pod Anti-Affinity? | 🚫 Keep Pods away from specific Pods.                |
| 3️⃣1️⃣ | Why use Pod Affinity?      | ⚡ Reduce network latency between related Pods.       |
| 3️⃣2️⃣ | Why use Pod Anti-Affinity? | 🛡️ Improve High Availability by spreading replicas. |
| 3️⃣3️⃣ | Common topology key?       | `kubernetes.io/hostname`                             |
| 3️⃣4️⃣ | What is Topology Spread Constraints? | 🌍 Evenly distributes Pods `across Nodes` or `Zones`.                       |
| 3️⃣5️⃣ | Why use it?                          | 🛡️ Improve `fault tolerance` and `availability`.                                |
| 3️⃣6️⃣ | Common topology keys?                | `kubernetes.io/hostname`, `topology.kubernetes.io/zone`                      |
| 3️⃣7️⃣ | What is `maxSkew`?                   | 📊 Maximum allowed difference in Pod count between domains.                  |
| 3️⃣8️⃣ | What is `whenUnsatisfiable`?         | 🚦 Action when constraints can't be met (`DoNotSchedule`, `ScheduleAnyway`). |
| 3️⃣9️⃣ | What is a PriorityClass?                                | ⭐ Assigns priority to Pods.                                                    |
| 4️⃣0️⃣ | What is Preemption?                                     | 🔄 High-priority Pods `evict lower-priority Pods` if resources are insufficient. |
| 4️⃣1️⃣ | Field to assign priority?                               | `priorityClassName`                                                            |
| 4️⃣2️⃣ | What is `globalDefault`?                                | 🌍 Makes the PriorityClass the default for Pods without `priorityClassName`.   |
| 4️⃣3️⃣ | Default value of `globalDefault`?                       | ❌ `false`                                                                      |
| 4️⃣4️⃣ | Can multiple PriorityClasses have `globalDefault=true`? | ❌ No                                                                           |
| 4️⃣5️⃣ | Higher `value` means?                                   | 📈 Higher scheduling priority.                                                 |
| 4️⃣6️⃣ | What is a Custom Scheduler?            | 🎛️ A `user-defined scheduler` for `specialized scheduling logic`. |
| 4️⃣7️⃣ | Why use a Custom Scheduler?            | ⚙️ Implement `custom scheduling policies`.                       |
| 4️⃣8️⃣ | How does a Pod use a Custom Scheduler? | Specify `schedulerName` in the Pod spec.                       |
| 4️⃣9️⃣ | Default scheduler name?                | `default-scheduler`                                            |
| 5️⃣0️⃣ | Can multiple schedulers run together?  | ✅ Yes                                                          |
| 5️⃣1️⃣ | What is the Descheduler?                     | 🧹 Rebalances Pods after scheduling decisions become suboptimal. |
| 5️⃣2️⃣ | Does the Descheduler replace kube-scheduler? | ❌ No                                                             |
| 5️⃣3️⃣ | When is the Descheduler useful?              | 🔄 After node additions, label changes, or resource imbalances.  |
| 5️⃣4️⃣ | Can the Descheduler evict Pods?              | ✅ Yes, based on configured policies.                             |
| 5️⃣5️⃣ | Does the Descheduler schedule Pods?          | ❌ No, `kube-scheduler schedules` them after eviction.              |


## 🎤 Most Asked Interview Questions
| ❓ Question                                                   | 🎯 Short Answer                                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------------------------------------ |
| Difference between NodeSelector and Node Affinity?           | 🏷️ NodeSelector is `simple label matching`; 🧩 Node Affinity supports `advanced expressions`. |
| Difference between Taints and Tolerations?                   | 🚫 Taints reject Pods; ✅ Tolerations allow Pods onto tainted Nodes. (Like a "permission slip" for a pod to land on a `tainted node`. Without it, the `pod is rejected`.) |
| Difference between Pod Affinity and Anti-Affinity?           | 🤝 Place Pods together vs 🚫 keep Pods apart.                                              |
| What is Topology Spread Constraints?                         | 🌍 Evenly spreads Pods `across Nodes` or `Zones`.                                              |
| What is PriorityClass?                                       | ⭐ Assigns scheduling priority to Pods.                                                     |
| What is Preemption?                                          | 🔄 High-priority Pods `evict lower-priority Pods`.                                           |
| What is `schedulerName`?                                     | 🎛️ Selects the scheduler for a Pod.                                                       |
| Difference between `default-scheduler` and Custom Scheduler? | 🤖 Default uses Kubernetes rules; 🎛️ Custom implements user-defined logic.                |
| What is the Descheduler?                                     | 🧹 Rebalances Pods after initial scheduling.                                               |
| Which component performs scheduling?                         | 🤖 **kube-scheduler**                                                                      |
