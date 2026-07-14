# Kubernetes Error 3: Pending Pods
## Cause
 * A Pod remains in the Pending state because Kubernetes is `unable to schedule it on any node`.

## Common Reasons
 * 💾 Insufficient CPU or memory on the cluster
 * 🚫 No available nodes
 * 💽 Unbound PersistentVolumeClaim (PVC)
 * 🏷️ Node Selector or Node Affinity mismatch
 * 🚧 Taints and Tolerations mismatch
 * 🔌 Image pull secrets or storage provisioning issues

## 🚀 Pending Pod Troubleshooting Flow
```hcl
Pod Status = Pending
        │
        ▼
kubectl describe pod <pod-name>
        │
        ▼
Check Scheduling Events
        │
        ▼
Enough CPU / Memory?
        │
        ▼
Node Selector / Affinity Match?
        │
        ▼
PVC Bound Successfully?
        │
        ▼
Taints & Tolerations Match?
        │
        ▼
StorageClass / CSI Driver Working?
        │
        ▼
Node Healthy & Ready?
```

## ☸️ Kubernetes Pending Pods — Common Reasons & Solutions
| 🚨 **Cause**                                   | 📖 **Description**                                      | 🛠️ **Solution**                                                     | 💻 **Useful Commands**                                     |
| ---------------------------------------------- | ------------------------------------------------------- | -------------------------------------------------------------------- | ---------------------------------------------------------- |
| 📍 **Pod Scheduling Failure**                  | Scheduler cannot place the Pod on any node              | Check Pod events to identify the scheduling reason                   | `kubectl describe pod <pod-name>`                          |
| 💾 **Insufficient CPU or Memory**              | Nodes don't have enough available resources             | Verify node capacity and resource usage; add nodes or free resources | `kubectl get nodes`<br>`kubectl describe node <node-name>` |
| 📊 **Resource Requests Exceed Capacity**       | CPU/Memory requests are larger than available resources | Reduce resource requests/limits or scale the cluster                 | `kubectl edit deployment <deployment-name>`                |
| 🏷️ **Node Selector / Node Affinity Mismatch**  | No node matches the Pod's scheduling rules              | Update or remove `nodeSelector` or affinity rules                    | `kubectl describe pod <pod-name>`                          |
| 💽 **PersistentVolumeClaim (PVC) Pending**     | Required storage is not available                       | Verify PVC, StorageClass, PV, and CSI Driver                         | `kubectl get pvc`<br>`kubectl describe pvc <pvc-name>`     |
| 🚧 **Taints and Tolerations Mismatch**         | Node taints prevent Pod scheduling                      | Add matching tolerations or use another node                         | `kubectl describe node <node-name>`                        |
| 🚫 **Unschedulable Node**                      | Node is cordoned or marked unschedulable                | Uncordon the node or schedule on another node                        | `kubectl get nodes`                                        |
| 📦 **Cluster Autoscaler Delay**                | New nodes have not been provisioned yet                 | Wait for autoscaling or add nodes manually                           | `kubectl get nodes`                                        |
| 🔌 **CSI Driver / Storage Issues**             | Storage provisioner cannot create a volume              | Check CSI driver status and StorageClass                             | `kubectl get storageclass`<br>`kubectl get csidrivers`     |
| ⚠️ **Resource Quota / LimitRange**             | Namespace quota or limits prevent scheduling            | Check namespace quotas and adjust limits                             | `kubectl describe resourcequota`                           |

## 🔍 𝗨𝘀𝗲𝗳𝘂𝗹 𝗧𝗿𝗼𝘂𝗯𝗹𝗲𝘀𝗵𝗼𝗼𝘁𝗶𝗻𝗴 𝗖𝗼𝗺𝗺𝗮𝗻𝗱𝘀
| 💻 **Command**                                | 🎯 **Purpose**                    |
| --------------------------------------------- | --------------------------------- |
| `kubectl get pods`                            | Check Pod status                  |
| `kubectl describe pod <pod-name>`             | View scheduling events            |
| `kubectl get nodes`                           | Check node status                 |
| `kubectl describe node <node-name>`           | Inspect node resources and taints |
| `kubectl top nodes`                           | Check CPU and memory utilization  |
| `kubectl get pvc`                             | Verify PVC status                 |
| `kubectl describe pvc <pvc-name>`             | Check why a PVC is pending        |
| `kubectl get storageclass`                    | Verify StorageClass configuration |
| `kubectl get events --sort-by=.lastTimestamp` | Review recent cluster events      |


## 🎯 Interview Answer
𝗤: 𝗛𝗼𝘄 𝗱𝗼 𝘆𝗼𝘂 𝘁𝗿𝗼𝘂𝗯𝗹𝗲𝘀𝗵𝗼𝗼𝘁 𝗮 𝗣𝗼𝗱 𝘀𝘁𝘂𝗰𝗸 𝗶𝗻 𝘁𝗵𝗲 𝗣𝗲𝗻𝗱𝗶𝗻𝗴 𝘀𝘁𝗮𝘁𝗲❓
 * Check the Pod events using 𝙠𝙪𝙗𝙚𝙘𝙩𝙡 𝙙𝙚𝙨𝙘𝙧𝙞𝙗𝙚 𝙥𝙤𝙙.
 * Verify node CPU and memory availability.
 * Review resource requests and limits.
 * Check 𝗻𝗼𝗱𝗲𝗦𝗲𝗹𝗲𝗰𝘁𝗼𝗿 and node affinity rules.
 * Verify that the PVC is bound and the StorageClass/CSI driver are working.
 * Inspect node taints and Pod tolerations.
 * Check node health and namespace quotas if necessary.

## 🎯 Interview One-Liner
 * A Pod remains in the Pending state when Kubernetes cannot schedule it onto a node.
 * The most common causes are 𝗶𝗻𝘀𝘂𝗳𝗳𝗶𝗰𝗶𝗲𝗻𝘁 𝗖𝗣𝗨 or 𝗺𝗲𝗺𝗼𝗿𝘆, 𝗲𝘅𝗰𝗲𝘀𝘀𝗶𝘃𝗲 𝗿𝗲𝘀𝗼𝘂𝗿𝗰𝗲 𝗿𝗲𝗾𝘂𝗲𝘀𝘁𝘀, 𝗻𝗼𝗱𝗲 𝘀𝗲𝗹𝗲𝗰𝘁𝗼𝗿 or 𝗮𝗳𝗳𝗶𝗻𝗶𝘁𝘆 𝗺𝗶𝘀𝗺𝗮𝘁𝗰𝗵𝗲𝘀, 𝗽𝗲𝗻𝗱𝗶𝗻𝗴 𝗣𝗩𝗖𝘀, 𝘁𝗮𝗶𝗻𝘁 𝗮𝗻𝗱 𝘁𝗼𝗹𝗲𝗿𝗮𝘁𝗶𝗼𝗻 𝗺𝗶𝘀𝗺𝗮𝘁𝗰𝗵𝗲𝘀, 𝘀𝘁𝗼𝗿𝗮𝗴𝗲 𝗽𝗿𝗼𝘃𝗶𝘀𝗶𝗼𝗻𝗶𝗻𝗴 𝗶𝘀𝘀𝘂𝗲𝘀, or 𝘂𝗻𝘀𝗰𝗵𝗲𝗱𝘂𝗹𝗮𝗯𝗹𝗲 𝗻𝗼𝗱𝗲𝘀.
 * The first step is always to inspect the scheduling events with 𝗸𝘂𝗯𝗲𝗰𝘁𝗹 𝗱𝗲𝘀𝗰𝗿𝗶𝗯𝗲 𝗽𝗼𝗱. ☸️🚀
