
## 9. schedulerName (💡 Custom Scheduler)

👉 Use custom scheduler instead of default  
```
schedulerName: my-custom-scheduler
```
### Simple Understanding
- 👉 Kubernetes uses default-scheduler by default
- 👉 You can override it with your own scheduler logic
- 👉 schedulerName lets you replace Kubernetes default scheduling logic with your own decision-making system.
- 💡Use Case: 💰 Cost-aware scheduling, ⚡Latency-aware scheduling  

## 10. Default Scheduler
##### 🎯 What is Default Scheduler?
👉 Default Scheduler is the built-in Kubernetes component that decides which node a Pod should run on.  ✔ Used unless custom scheduler specified  
### 📄 YAML Example (Using Default Scheduler)
👉 You don’t need to define anything — it’s used automatically:
```
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

👉 Descheduler is a Kubernetes component that rebalances Pods after they are already running. It evicts poorly placed Pods so the scheduler can place them better.”  
### 📄 Descheduler Policy YAML
```
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
- LowNodeUtilization : 👉 Moves Pods from overloaded nodes → underutilized nodes


| **Use Case**    | **What Happens**                                   |
| --------------- | -------------------------------------------------- |
| Node changes    | Pods moved if nodes become unhealthy or overloaded |
| Cluster scaling | Redistributes Pods when new nodes are added        |

### 🎯 Real-life Analogy 
👉 “Think of it like seating in a classroom:
- Teacher (Scheduler) assigns seats initially
- Later, if students are unevenly distributed (Descheduler)”

### 👉 “Scheduler places Pods first, Descheduler fixes them later.”

---

## 12. DaemonSet (📦 One Pod Per Node)
👉 Ensures one Pod runs on every node  

💡 Use Case:
- Logging agents 📜
- Monitoring tools 📊 

---

### 🧪 Real-Life Examples

- GPU workloads → nodeSelector / taints  
- Microservices → podAffinity  
- HA systems → podAntiAffinity  
- Multi-zone apps → topologySpreadConstraints  

---

## 🔧 Important Commands
```
kubectl get pods                     # 📦 List all pods
kubectl get pods -o wide             # 📦 Pods with node info
kubectl get nodes                    # 🖥️ List nodes
kubectl describe node <node-name>    # 🖥️ Node details (labels, taints, capacity)

# 🏷️ Labels & Node Selection
kubectl label nodes <node-name> disktype=ssd          # Add label to node
kubectl get nodes --show-labels                       # View node labels


# Add taint to node
kubectl taint nodes <node-name> node-type=infra:NoSchedule

# Remove taint
kubectl taint nodes <node-name> node-type=infra:NoSchedule-

# View taints
kubectl describe node <node-name> | grep Taints


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

## 🧠 Quick Revision

- nodeSelector → Simple placement  
- nodeAffinity → Advanced rules  
- podAffinity → Together  
- podAntiAffinity → Separate  
- taints → Block  
- tolerations → Allow  
- topologySpread → Balance  
- priority → Importance  

---

### 🎯 One-Line Answer

Advanced scheduling in Kubernetes allows precise control over Pod placement using affinity rules, taints, topology, and priority mechanisms.
