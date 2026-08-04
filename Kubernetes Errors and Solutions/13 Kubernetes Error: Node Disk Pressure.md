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











