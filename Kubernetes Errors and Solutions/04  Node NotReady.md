# 🚨 Kubernetes Error 4: Node NotReady
 * A Kubernetes node enters the 𝓝𝓸𝓽𝓡𝓮𝓪𝓭𝔂 state when it cannot communicate properly with the `control plane` or has `resource/system issues`.

## Common reasons include:
 * 💾 Disk Pressure
 * 🧠 Memory Pressure
 * 🔥 PID Pressure (𝙋𝙧𝙤𝙘𝙚𝙨𝙨 𝙄𝘿𝙨 𝙖𝙧𝙚 𝙚𝙭𝙝𝙖𝙪𝙨𝙩𝙚𝙙)
 * 🌐 Network connectivity issues
 * ⚙️ Kubelet service stopped or unhealthy
 * 🔌 Container runtime (𝐃𝐨𝐜𝐤𝐞𝐫/𝐜𝐨𝐧𝐭𝐚𝐢𝐧𝐞𝐫𝐝) failure

## ✅ Check the node status
 * 𝙠𝙪𝙗𝙚𝙘𝙩𝙡 𝙜𝙚𝙩 𝙣𝙤𝙙𝙚𝙨
```hcl
NAME      STATUS     ROLES           AGE   VERSION
worker1   NotReady   <none>          15d   v1.31.0
```

## 🚀 Kubernetes Node NotReady Troubleshooting Flow
```hcl
                 Node Status = NotReady
                          │
                          ▼
          kubectl describe node <node-name>
                          │
                          ▼
               Check Node Conditions
                          │
        ┌─────────────────┼─────────────────┐
        ▼                 ▼                 ▼
  DiskPressure?     MemoryPressure?    PIDPressure?
        │                 │                 │
        ▼                 ▼                 ▼
 Free Disk Space    Free Memory /     Reduce Processes /
 Clean Images       Increase Capacity Increase PID Limit
        │                 │                 │
        └─────────────────┴─────────────────┘
                          │
                          ▼
            Check Kubelet Service Running?
                          │
                          ▼
        sudo systemctl status kubelet
                          │
                  Running?
                 /        \
              No          Yes
              │            │
              ▼            ▼
 sudo systemctl     Check Container Runtime
 restart kubelet            │
                            ▼
          sudo systemctl status containerd
                            │
                    Running?
                   /        \
                No          Yes
                │            │
                ▼            ▼
      Restart containerd   Check Network
                            │
                            ▼
     Can node reach Kubernetes API Server?
                            │
                   Yes               No
                    │                 │
                    ▼                 ▼
          Check Node Events     Fix Network/DNS/
          kubectl get events    Firewall/Routing
                    │
                    ▼
               Node Ready ✅
```


## ☸️ 𝐊𝐮𝐛𝐞𝐫𝐧𝐞𝐭𝐞𝐬 𝐍𝐨𝐝𝐞 𝐍𝐨𝐭𝐑𝐞𝐚𝐝𝐲 — 𝐂𝐨𝐦𝐦𝐨𝐧 𝐑𝐞𝐚𝐬𝐨𝐧𝐬 & 𝐒𝐨𝐥𝐮𝐭𝐢𝐨𝐧𝐬
| 🚨 **Cause**                     | 📖 **Description**                             | 🛠️ **Solution**                                                   | 💻 **Useful Commands**                                |
| -------------------------------- | ---------------------------------------------- | ------------------------------------------------------------------ | ----------------------------------------------------- |
| 💾 **Disk Pressure**             | Node is running out of disk space              | Free disk space, remove unused images/logs, or expand the disk     | `kubectl describe node <node-name>`<br>`df -h`        |
| 🧠 **Memory Pressure**           | Node has insufficient available memory         | Stop unnecessary processes, optimize workloads, or add memory      | `kubectl describe node <node-name>`<br>`free -h`      |
| ⚡ **PID Pressure**               | Node has exhausted available process IDs       | Reduce running processes or increase the PID limit                 | `kubectl describe node <node-name>`                   |
| 🌐 **Network Issue**             | Node cannot communicate with the control plane | Verify network connectivity, security groups, firewall, and routes | `ping` / `curl` / `kubectl describe node`             |
| 🤖 **Kubelet Not Running**       | Kubelet service has stopped or failed          | Restart kubelet and inspect its logs                               | `systemctl status kubelet`<br>`journalctl -u kubelet` |
| 🐳 **Container Runtime Failure** | Docker/containerd is not running               | Restart the container runtime and review logs                      | `systemctl status containerd`                         |
| 🔐 **Certificate Expired**       | Kubelet certificate has expired                | Renew the certificate and restart kubelet                          | `kubeadm certs renew` (kubeadm clusters)              |
| ☁️ **Cloud Provider Issue**      | EC2 instance or cloud networking problem       | Verify the VM is healthy and networking is configured correctly    | Check cloud console and instance status               |
| 🖥️ **Node Shutdown/Reboot**     | Node has restarted or is powered off            | Bring the node back online and verify services                     | `kubectl get nodes`                                   |
| ⚙️ **High CPU/Memory Usage**     | Node is overloaded                             | Reduce workload, add nodes, or enable Cluster Autoscaler           | `kubectl top node`                                    |

## 🔍 Useful Troubleshooting Commands
| 💻 **Command**                      | 🎯 **Purpose**                  |
| ----------------------------------- | ------------------------------- |
| `kubectl get nodes`                 | Check node status               |
| `kubectl describe node <node-name>` | View node conditions and events |
| `kubectl top node`                  | Check CPU and memory usage      |
| `systemctl status kubelet`          | Verify kubelet service          |
| `journalctl -u kubelet -f`          | View kubelet logs               |
| `systemctl status containerd`       | Verify container runtime        |
| `df -h`                             | Check disk usage                |
| `free -h`                           | Check memory usage              |

## 🎯 Interview Answer
𝗤: 𝗛𝗼𝘄 𝗱𝗼 𝘆𝗼𝘂 𝘁𝗿𝗼𝘂𝗯𝗹𝗲𝘀𝗵𝗼𝗼𝘁 𝗮 𝗡𝗼𝗱𝗲 𝗡𝗼𝘁𝗥𝗲𝗮𝗱𝘆 𝗶𝘀𝘀𝘂𝗲❓
 * Run 𝐤𝐮𝐛𝐞𝐜𝐭𝐥 𝐠𝐞𝐭 𝐧𝐨𝐝𝐞𝐬 to identify the affected node.
 * Use 𝐤𝐮𝐛𝐞𝐜𝐭𝐥 𝐝𝐞𝐬𝐜𝐫𝐢𝐛𝐞 𝐧𝐨𝐝𝐞 <𝐧𝐨𝐝𝐞-𝐧𝐚𝐦𝐞> to inspect node conditions and events.
 * Check for disk, memory, or PID pressure (Process IDs are exhausted).
 * Verify that the 𝙠𝙪𝙗𝙚𝙡𝙚𝙩 and 𝙘𝙤𝙣𝙩𝙖𝙞𝙣𝙚𝙧 𝙧𝙪𝙣𝙩𝙞𝙢𝙚 are running.
 * Check network connectivity between the node and the control plane.
 * Review kubelet logs and resolve any underlying infrastructure or resource issues.

### 🎯 Interview One-Liner
 * A Node NotReady status means the control plane considers the 𝐧𝐨𝐝𝐞 𝐮𝐧𝐡𝐞𝐚𝐥𝐭𝐡𝐲 or 𝐮𝐧𝐫𝐞𝐚𝐜𝐡𝐚𝐛𝐥𝐞.
 * Common causes include disk pressure, memory pressure, PID exhaustion, kubelet or container runtime failures, network connectivity issues, certificate problems, or cloud infrastructure issues.
 * The first troubleshooting step is to inspect the node using 𝐤𝐮𝐛𝐞𝐜𝐭𝐥 𝐝𝐞𝐬𝐜𝐫𝐢𝐛𝐞 𝐧𝐨𝐝𝐞 <𝐧𝐨𝐝𝐞-𝐧𝐚𝐦𝐞>.
