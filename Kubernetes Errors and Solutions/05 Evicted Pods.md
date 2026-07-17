# 🚨 Kubernetes Error: Evicted Pods
### 📌 What is an Evicted Pod?
 * An Evicted Pod is a pod that Kubernetes forcibly removes from a node because the `node doesn't have enough resources` to keep it running.

## 🚀 Evicted Pod Troubleshooting Flow
```hcl
Pod Status = Evicted
        ↓
kubectl describe pod <pod-name>
        ↓
Check Eviction Reason
        ↓
MemoryPressure?
        ↓
DiskPressure?
        ↓
Ephemeral Storage Full?
        ↓
Check Node Resources
(kubectl top node)
        ↓
Free Resources or Increase Capacity
        ↓
Adjust Resource Requests/Limits
        ↓
Delete Evicted Pod
        ↓
New Pod Created Successfully ✅
```

## ☸️ Kubernetes Pod Eviction — Common Reasons & Solutions
| 🚨 **Cause**                      | 📖 **Description**                                  | 🛠️ **Solution**                                                      | 💻 **Useful Commands**                         |
| --------------------------------- | --------------------------------------------------- | --------------------------------------------------------------------- | ---------------------------------------------- |
| 💾 **Disk Pressure**              | Node disk is almost full                            | Free disk space, remove unused images/logs, or expand the disk        | `kubectl describe node <node-name>`<br>`df -h` |
| 🧠 **Memory Pressure**            | Node runs out of RAM                                | Free memory, optimize workloads, or add more memory/nodes             | `kubectl top node`<br>`free -h`                |
| 🔥 **Ephemeral Storage Full**     | `/var/lib/containerd` or `/var/lib/kubelet` is full | Clean unused images, containers, and logs or increase storage         | `df -h`<br>`crictl images`                     |
| ⚙️ **Node Resource Exhaustion**   | CPU, memory, or storage exceeds node capacity       | Scale the cluster, redistribute workloads, or optimize resource usage | `kubectl top node`                             |
| 🚨 **Resource Requests Too High** | Pods request more resources than available          | Reduce CPU/memory requests or add more nodes                          | `kubectl describe pod <pod-name>`              |
| 📦 **Too Many Pods on Node**      | Node reaches its maximum Pod capacity               | Schedule Pods on other nodes or scale the cluster                     | `kubectl describe node <node-name>`            |

## 🔍 Useful Troubleshooting Commands
| 💻 Command                                                 | 🎯 Purpose                                                        |
| ---------------------------------------------------------- | ----------------------------------------------------------------- |
| `kubectl get pods`                                         | List pods and identify evicted pods                               |
| `kubectl describe pod <pod-name>`                          | View eviction reason and events                                   |
| `kubectl top node`                                         | Check node CPU and memory usage                                   |
| `kubectl describe node <node-name>`                        | Check node conditions (MemoryPressure, DiskPressure, PIDPressure) |
| `kubectl top pod`                                          | Check pod resource usage                                          |
| `kubectl get events --sort-by=.metadata.creationTimestamp` | View recent cluster events                                        |
| `kubectl delete pod <pod-name>`                            | Remove an evicted pod                                             |







