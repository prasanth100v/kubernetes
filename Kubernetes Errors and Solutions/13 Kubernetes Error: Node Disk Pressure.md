# 🚨 Kubernetes Error: Node Disk Pressure — Common Reasons & Solutions
## 📌 What is Node Disk Pressure?
 * Node Disk Pressure occurs when a Kubernetes node is running low on disk space. To protect the node, Kubernetes marks it with the DiskPressure condition and may stop scheduling new pods or evict existing pods.
 * Example: `STATUS: DiskPressure=True`

## ☸️ Kubernetes Node Disk Pressure — Common Reasons & Solutions
| 🚨 **Cause**                        | 📖 **Description**                                  | 🛠️ **Solution**                                        | 💻 **Useful Commands**                         |
| ----------------------------------- | --------------------------------------------------- | ------------------------------------------------------- | ---------------------------------------------- |
| 💾 **Disk Almost Full**             | Root filesystem has very little free space          | Free disk space or expand the disk                      | `df -h`                                        |
| 🐳 **Too Many Container Images**    | Unused Docker/containerd images consume disk space  | Remove unused images                                    | `crictl rmi --prune` *(containerd)*            |
| 📜 **Large Container Logs**         | Container log files have grown significantly        | Rotate or delete old logs                               | `du -sh /var/log`                              |
| 📦 **Unused Containers**            | Stopped containers still occupy disk                | Remove unused containers                                | `crictl ps -a`<br>`crictl rm <container-id>`   |
| 💽 **Large Persistent Data**        | Application data or mounted volumes consume storage | Clean unnecessary data or expand storage                | `du -sh /var/lib/*`                            |
| 📈 **High Ephemeral Storage Usage** | Pods consume excessive temporary storage            | Limit ephemeral storage usage and clean temporary files | `kubectl top pod` *(if metrics are available)* |

## 🚀 Node Disk Pressure Troubleshooting Flow
```hcl
Pods Evicted / Node Not Scheduling
              ↓
kubectl describe node <node-name>
              ↓
DiskPressure=True?
              ↓
Check Disk Usage (df -h)
              ↓
Find Large Files & Images
              ↓
Clean Unused Images, Containers & Logs
              ↓
Enough Free Space?
        ↓               ↓
       Yes              No
        ↓               ↓
Verify Node       Increase Disk /
Recovery          Add More Nodes
        ↓
DiskPressure=False
        ↓
Node Ready ✅
```

## 🔍 Useful Troubleshooting Commands
| 💻 Command                                                 | 🎯 Purpose                                  |
| ---------------------------------------------------------- | ------------------------------------------- |
| `kubectl get nodes`                                        | List all cluster nodes                      |
| `kubectl describe node <node-name>`                        | Check node conditions and events            |
| `df -h`                                                    | Check disk usage                            |
| `du -sh /var/lib/*`                                        | Find large directories                      |
| `docker system prune -f`                                   | Remove unused Docker resources              |
| `crictl images`                                            | List container images                      |
| `crictl rmi --prune`                                       | Remove unused container images (containerd) |
| `kubectl top node`                                         | View node resource usage                    |
| `kubectl get events --sort-by=.metadata.creationTimestamp` | View recent cluster events                  |
| `crictl ps -a`                      | List all containers                        |
| `journalctl --disk-usage`           | Check systemd journal disk usage           |

# 🎯 Interview Answers
## Q: How do you troubleshoot a node with DiskPressure=True?
 * Check the node condition using `kubectl describe node <node-name>`.
 * Verify disk usage with `df -h`.
 * Identify large directories using `du -sh`.
 * Remove unused container images and stopped containers.
 * Clean up old container and system logs.
 * Check ephemeral storage usage by Pods.
 * Expand the node's disk or add more nodes if required.

## 🎯 Simple Answer
 * ☸️ `DiskPressure=True` indicates that a Kubernetes node is running low on available disk space.
 * 🚀 Common causes include a nearly full root filesystem, unused container images, large log files, stopped containers, excessive persistent data, and high ephemeral storage usage.
 * 🚀 The first troubleshooting step is to inspect the node with `kubectl describe node` and check disk utilization using `df -h`.

## 💡 Q: What is Node Disk Pressure in Kubernetes, and how do you troubleshoot it?
 * Node Disk Pressure occurs when a worker node runs low on available disk space.
 * Kubernetes marks the node with the DiskPressure=True condition, may stop scheduling new pods, and can evict existing pods to protect node stability.
 * I troubleshoot it by checking the node status with kubectl describe node, verifying disk usage using df -h, identifying large files or directories, cleaning up unused container images, containers, and logs, and checking ephemeral storage usage.
 * If disk pressure persists, I increase the node's storage capacity or add more worker nodes to the cluster.
