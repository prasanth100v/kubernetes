
## 9. schedulerName (🧠 Custom Scheduler)

👉 Use custom scheduler instead of default  
```
schedulerName: my-custom-scheduler
```
💡 Use Case:
- Cost-aware scheduling 💰
- Latency-aware scheduling ⚡ 



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
