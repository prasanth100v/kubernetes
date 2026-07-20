# 🚨 Kubernetes Error: Pod Cannot Access PersistentVolume 
## 📌 What is a PersistentVolume (PV) and PersistentVolumeClaim (PVC)?
 * PersistentVolume (PV) is a 𝘀𝘁𝗼𝗿𝗮𝗴𝗲 𝗿𝗲𝘀𝗼𝘂𝗿𝗰𝗲 in the Kubernetes cluster.
 * PersistentVolumeClaim (PVC) is a 𝙧𝙚𝙦𝙪𝙚𝙨𝙩 for storage made by a pod.
 * A pod can only use persistent storage when the PVC is successfully bound to a PV.
 * If the PVC is not bound, the pod may remain in the 𝗣𝗲𝗻𝗱𝗶𝗻𝗴 state.
 * 𝔼𝕩𝕒𝕞𝕡𝕝𝕖:
     * STATUS: 𝗣𝗲𝗻𝗱𝗶𝗻𝗴  (or)
     * Warning  𝗙𝗮𝗶𝗹𝗲𝗱𝗦𝗰𝗵𝗲𝗱𝘂𝗹𝗶𝗻𝗴 / (pod has unbound immediate PersistentVolumeClaims)..

## ✅ Check the PVC Status
```hcl
kubectl get pvc
---
NAME         STATUS    VOLUME   CAPACITY   STORAGECLASS
mysql-pvc    🅿🅴🅽🅳🅸🅽🅶                                    # If the PVC status is Pending, it is not bound to a PV.
```

## 🚀 PersistentVolume Troubleshooting Flow
```hcl
Pod Cannot Access Storage
          ↓
kubectl get pvc
          ↓
PVC Status = Pending?
          ↓
kubectl describe pvc
          ↓
StorageClass Matches?
          ↓
PV Available?
          ↓
Access Modes Match?
          ↓
CSI Driver Running?
          ↓
Verify Pod Uses Correct PVC
          ↓
PVC Bound Successfully
          ↓
Pod Starts Successfully ✅
```

## ☸️ Kubernetes PV/PVC Issues — Common Reasons & Solutions
| 🚨 **Cause**                     | 📖 **Description**                                                           | 🛠️ **Solution**                                                      | 💻 **Useful Commands**                                 |
| -------------------------------- | ---------------------------------------------------------------------------- | --------------------------------------------------------------------- | ------------------------------------------------------ |
| 📦 **PVC in Pending State**      | No matching PersistentVolume (PV) is available                               | Create a matching PV or ensure dynamic provisioning is working        | `kubectl get pvc`<br>`kubectl describe pvc <pvc-name>` |
| 💾 **StorageClass Mismatch**     | PVC requests a different `StorageClass` than the PV provides                 | Update the PVC or PV to use the same `StorageClass`                   | `kubectl get storageclass`                             |
| 📏 **Size Mismatch**             | PVC requests more storage than the PV capacity                               | Increase the PV size or reduce the PVC request                        | `kubectl describe pv <pv-name>`                        |
| 🔄 **Access Mode Mismatch**      | Access modes (`ReadWriteOnce`, `ReadOnlyMany`, `ReadWriteMany`) do not match | Configure compatible access modes on the PV and PVC                   | `kubectl describe pv <pv-name>`                        |
| ❌ **PV Not Created**            | No PersistentVolume exists (static provisioning)                             | Create a PV or enable dynamic provisioning with a `StorageClass`      | `kubectl get pv`                                       |
| ☁️ **CSI Driver Not Installed**  | Storage driver (AWS EBS, Azure Disk, GCE PD, etc.) is missing or unhealthy   | Install or verify the CSI driver and its controller/node Pods         | `kubectl get csidrivers`                               |
| 🔗 **Incorrect PVC Reference**   | Pod references the wrong PVC name                                            | Update the Pod or Deployment to reference the correct PVC             | `kubectl describe pod <pod-name>`                      |
| 🚫 **Volume Attachment Failure** | Cloud volume cannot attach to the node                                       | Verify node health, cloud provider, and CSI controller logs           | `kubectl describe pvc <pvc-name>`                      |
| 🗑️ **Released or Failed PV**     | PV is in `Released` or `Failed` state                                        | Reclaim, recreate, or clean up the PV according to the reclaim policy | `kubectl get pv`                                       |

## 🔍 Useful Troubleshooting Commands
| 💻 **Command**                    | 🎯 **Purpose**                      |
| --------------------------------- | ----------------------------------- |
| `kubectl get pvc`                 | Check PVC status                    |
| `kubectl describe pvc <pvc-name>` | View PVC events and binding issues  |
| `kubectl get pv`                  | List PersistentVolumes              |
| `kubectl describe pv <pv-name>`   | Inspect PV details                  |
| `kubectl get storageclass`        | Verify StorageClass configuration   |
| `kubectl get csidrivers`          | Check installed CSI drivers         |
| `kubectl get pods -n kube-system` | Verify CSI controller and node Pods |
| `kubectl describe pod <pod-name>` | Check volume mount errors           |
| `kubectl describe pod <pod-name>`                          | Check pod volume-related errors    |
| `kubectl get deployment <deployment-name> -o yaml`         | Verify PVC references              |
| `kubectl get events --sort-by=.metadata.creationTimestamp` | View recent cluster events         |

## 🎯 Interview Answer
### Q: How do you troubleshoot PV/PVC issues in Kubernetes?
 * Check the PVC status using 𝙠𝙪𝙗𝙚𝙘𝙩𝙡 𝙜𝙚𝙩 𝙥𝙫𝙘 and 𝙠𝙪𝙗𝙚𝙘𝙩𝙡 𝙙𝙚𝙨𝙘𝙧𝙞𝙗𝙚 𝙥𝙫𝙘.
 * Verify that the PVC is bound to a PV.
 * Ensure the StorageClass matches between the PV and PVC.
 * Confirm the 𝗿𝗲𝗾𝘂𝗲𝘀𝘁𝗲𝗱 𝘀𝘁𝗼𝗿𝗮𝗴𝗲 𝘀𝗶𝘇𝗲 and 𝗮𝗰𝗰𝗲𝘀𝘀 𝗺𝗼𝗱𝗲𝘀 are compatible.
 * Verify that the CSI driver is installed and healthy for dynamic provisioning.
 * Check that the Pod references the correct PVC.
 * Review Pod events for 𝙫𝙤𝙡𝙪𝙢𝙚 𝙖𝙩𝙩𝙖𝙘𝙝𝙢𝙚𝙣𝙩 or 𝙢𝙤𝙪𝙣𝙩 𝙛𝙖𝙞𝙡𝙪𝙧𝙚𝙨.

## 🎯 Interview One-Liner
 * ☸️ PV/PVC issues are commonly caused by 𝙥𝙚𝙣𝙙𝙞𝙣𝙜 𝙋𝙑𝘾𝙨, 𝙎𝙩𝙤𝙧𝙖𝙜𝙚𝘾𝙡𝙖𝙨𝙨 𝙢𝙞𝙨𝙢𝙖𝙩𝙘𝙝𝙚𝙨, 𝙞𝙣𝙨𝙪𝙛𝙛𝙞𝙘𝙞𝙚𝙣𝙩 𝙋𝙑 𝙘𝙖𝙥𝙖𝙘𝙞𝙩𝙮, 𝙞𝙣𝙘𝙤𝙢𝙥𝙖𝙩𝙞𝙗𝙡𝙚 𝙖𝙘𝙘𝙚𝙨𝙨 𝙢𝙤𝙙𝙚𝙨, 𝙢𝙞𝙨𝙨𝙞𝙣𝙜 𝙋𝙑𝙨, 𝙪𝙣𝙝𝙚𝙖𝙡𝙩𝙝𝙮 𝘾𝙎𝙄 𝙙𝙧𝙞𝙫𝙚𝙧𝙨, or 𝙞𝙣𝙘𝙤𝙧𝙧𝙚𝙘𝙩 𝙋𝙑𝘾 𝙧𝙚𝙛𝙚𝙧𝙚𝙣𝙘𝙚𝙨.
 * 🚀 The first troubleshooting step is to inspect the PVC with 𝗸𝘂𝗯𝗲𝗰𝘁𝗹 𝗱𝗲𝘀𝗰𝗿𝗶𝗯𝗲 𝗽𝘃𝗰, then verify the PV, StorageClass, CSI driver, and Pod volume configuration.
