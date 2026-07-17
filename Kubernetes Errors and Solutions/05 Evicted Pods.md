# 🚨 Kubernetes Error: Evicted Pods
### 📌 What is an Evicted Pod❓
 * An Evicted Pod is a pod that Kubernetes forcibly removes from a node because the `node doesn't have enough resources` to keep it running.

### ✅ Identify Evicted Pods
```hcl
kubectl get pods

---

NAME          READY   STATUS     RESTARTS   AGE
nginx-abc     0/1     𝔼𝕧𝕚𝕔𝕥𝕖𝕕    0          5m
```

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

# 🎯 Interview Answer
## How do you troubleshoot an Evicted Pod❓
 * Run 𝗸𝘂𝗯𝗲𝗰𝘁𝗹 𝗱𝗲𝘀𝗰𝗿𝗶𝗯𝗲 𝗽𝗼𝗱 <𝗽𝗼𝗱-𝗻𝗮𝗺𝗲> to identify the eviction reason.
 * Check node conditions using 𝙠𝙪𝙗𝙚𝙘𝙩𝙡 𝙙𝙚𝙨𝙘𝙧𝙞𝙗𝙚 𝙣𝙤𝙙𝙚 <𝙣𝙤𝙙𝙚-𝙣𝙖𝙢𝙚>.
 * Verify disk, memory, and ephemeral storage usage.
 * Review `CPU` and `memory` utilization with 𝗸𝘂𝗯𝗲𝗰𝘁𝗹 𝘁𝗼𝗽 𝗻𝗼𝗱𝗲 and 𝗸𝘂𝗯𝗲𝗰𝘁𝗹 𝘁𝗼𝗽 𝗽𝗼𝗱.
 * Clean up unused images/logs, optimize resource requests, or add more nodes if needed.

## 🎯 Interview One-Liner
 * ☸️ A Pod is evicted when the kubelet removes it to protect node stability, typically due to disk pressure, memory pressure, ephemeral storage exhaustion, or overall resource exhaustion.
 * 🚀 The first step is to inspect the eviction reason with 𝗸𝘂𝗯𝗲𝗰𝘁𝗹 𝗱𝗲𝘀𝗰𝗿𝗶𝗯𝗲 𝗽𝗼𝗱, then check node conditions and resource usage before freeing resources or scaling the cluster. 
