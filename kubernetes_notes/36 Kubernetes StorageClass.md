# 🧱 Kubernetes StorageClass 
# 📦 What is StorageClass?

 * A **StorageClass** in Kubernetes defines:
     * 👉 *How storage is dynamically provisioned* when a **PersistentVolumeClaim (PVC)** is created.
     * Acts like a **template for creating storage**
     * Defines performance, type, and behavior  

## 🚀 Key Benefits
 * Automatically creates **PersistentVolumes (PVs)**
 * Removes manual PV creation
 * Ideal for **cloud environments (`AWS`, `GCP`, `Azure`)**
 * Simplifies storage management  

## 🔧 Why StorageClass is Needed

  * ❌ Without StorageClass
     * You must manually create PVs
     * Hard to scale
     * Time-consuming  

  * ✅ With StorageClass
     * Kubernetes auto-creates storage
     * Faster and scalable
     * Fully automated  

## 🧠 How It Works

```
PVC Created
     ↓
StorageClass Referenced
     ↓
Provisioner Creates Volume
     ↓
PV Created Automatically
     ↓
Bound to PVC
     ↓
Used by Pod
```

## ⚙️ Key Components of StorageClass

---

## 🔌 Kubernetes CSI Provisioners (Cloud Providers)
  Defines the storage backend
  
| 🌍 Provider | ⚙️ Provisioner          |
| ----------- | ----------------------- |
| ☁️ AWS      | `ebs.csi.aws.com`       |
| 🌐 GCP      | `pd.csi.storage.gke.io` |
| 🔷 Azure    | `disk.csi.azure.com`    |
| 📂 NFS      | `nfs.csi.k8s.io`        |


## ⚙️ Parameters

 * Define storage configuration
     * Disk type (`gp3`, `standard`, etc.)
     * Performance (`IOPS`, `throughput`)
     * Filesystem (`ext4`, `xfs`)  

## ♻️ Reclaim Policy

| 🧩 **Policy**  | 💡 **Behavior**                  | 🧠 **How It Works**                                                                                              | 🎯 **When to Use**                     |
| -------------- | -------------------------------- | ---------------------------------------------------------------------------------------------------------------- | -------------------------------------- |
| 🛡️ **Retain** | 💾 Keeps data after PVC deletion | 👉 When PVC is deleted, the underlying volume **is NOT removed**<br>👉 Data remains and must be cleaned manually | 🗄 Critical data (databases, backups)  |
| 🗑️ **Delete** | ⚡ Deletes storage automatically  | 👉 When PVC is deleted, Kubernetes **deletes the actual storage** (EBS/EFS, etc.)                                | 🧪 Temporary or non-critical workloads |


## ⏱ volumeBindingMode

| 🔧 **Mode**                | 📖 **Description**                              | 🧠 **How It Works**                                                            | 💡 **Real-World Impact**                                           |
| -------------------------- | ----------------------------------------------- | ------------------------------------------------------------------------------ | ------------------------------------------------------------------ |
| ⚡ **Immediate**            | Volume is created instantly when PVC is created | 👉 PV is provisioned right away, before Pod scheduling                         | ⚠️ Can cause issues in multi-AZ setups (wrong zone selection)      |
| ⏳ **WaitForFirstConsumer** | Volume is created only after Pod is scheduled   | 👉 Kubernetes waits → schedules Pod → then creates volume in correct node/zone | ✅ Ensures correct placement (best for cloud environments like AWS) |

👉 **Recommended: WaitForFirstConsumer**

---

# 📦 StorageClass Example

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-sc
provisioner: ebs.csi.aws.com
parameters:
  type: gp3
  fsType: ext4

reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

---

# 🔰 Main StorageClass Types

| ⚙️ **Provisioner**           | 📖 **Description**                       | ☁️ **Platform** | 🧠 **How It Works**                                       | 💡 **Best Use Case**                          |
| ---------------------------- | ---------------------------------------- | ----------------- | --------------------------------------------------------- | --------------------------------------------- |
| 🗄 **ebs.csi.aws.com**       | AWS Elastic Block Store (EBS) CSI driver  | AWS ✅           | 👉 Creates block storage volumes attached to EC2 nodes    | 🗄 Databases, single-node apps (RWO)           |
| 💽 **pd.csi.storage.gke.io** | Google Compute Engine Persistent Disk    | GCP ✅           | 👉 Provides block storage for GKE clusters                | 📊 Stateful apps on GCP                       |
| 💾 **disk.csi.azure.com**    | Azure Managed Disks                      | Azure ✅         | 👉 Block storage attached to Azure VMs                    | 🗄 Databases on AKS                           |
| 🌐 **nfs.csi.k8s.io**        | Network File System (NFS) CSI            | Multi-node 🌍   | 👉 Shared file system accessible by multiple Pods         | 🌐 Shared storage (RWX), logs, media          |
| 🧪 **csi-hostpath**          | Local hostPath-based CSI driver          | Dev only ⚠️     | 👉 Uses node local filesystem for testing                 | 🧪 Local development/testing                  |
| 🖥 **local-storage**         | Manual local disk provisioning            | On-prem 🏢      | 👉 Uses physical disks on nodes (no dynamic provisioning) | 🏗 On-prem workloads needing high performance   |

---

# ✅ CSI (Container Storage Interface)

## 🚀 Why CSI?
 * Supports advanced features
 * Dynamic provisioning
 * Snapshots & cloning
 * Better scalability
 * 🔥 Examples :
     * AWS EBS CSI
     * Azure Disk CSI
     * GCP PD CSI  

---

# 🔁 Static vs Dynamic Provisioning

| ⚙️ **Type**                 | 📖 **Description**                                                | 🧠 **How It Works**                                                                                      | 💡 **Best Use Case**                                         |
| --------------------------- | ----------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| 🛠 **Static Provisioning**  | Admin manually creates a **PersistentVolume (PV)**                | 👉 Storage is created first by admin<br>👉 Pod/PVC later binds that existing volume                     | Good when storage must be pre-created and tightly controlled |
| 🤖 **Dynamic Provisioning** | Kubernetes automatically creates a **PV** when a PVC is requested | 👉 PVC uses a **StorageClass**<br>👉 Kubernetes talks to CSI driver and provisions storage automatically | Best for most modern cloud (AWS, GCP, Azure)<br>production environments       |


✔ StorageClass enables **dynamic provisioning**

---

# ⚠️ Important Notes

✔ Only **PersistentVolumes (PV)** preserve data  
❌ emptyDir, hostPath → Not persistent  

✔ You can mount multiple volumes:

```yaml
volumeMounts:
- name: config
  mountPath: /config

- name: logs
  mountPath: /logs
```

---

# ☁️ AWS EBS: Old vs New

---

## ❌ kubernetes.io/aws-ebs (In-tree)

- Built into Kubernetes  
- Deprecated  
- Limited features  

---

## ✅ ebs.csi.aws.com (CSI Driver)

✔ Modern & recommended  
✔ Supports resizing, snapshots  
✔ Uses IAM Roles (IRSA)  

---

## ⚠️ Important in EKS

👉 CSI driver is **NOT installed by default**  
👉 You must install it manually  

---

# 🧾 Useful kubectl Commands
## 📦 Kubernetes Storage Commands Cheat Sheet

```
# ================================
# 📦 PERSISTENT VOLUMES (PV)
# ================================
kubectl get pv                         # 📋 List all PersistentVolumes
kubectl describe pv <pv-name>         # 🔍 Show detailed PV info
kubectl delete pv <pv-name>           # ❌ Delete a PersistentVolume


# ================================
# 📁 PERSISTENT VOLUME CLAIMS (PVC)
# ================================
kubectl get pvc                        # 📋 List all PVCs
kubectl get pvc -n <namespace>         # 📂 PVCs in specific namespace
kubectl describe pvc <pvc-name>        # 🔍 Show PVC details
kubectl apply -f pvc.yaml              # ⚙️ Create PVC using YAML
kubectl delete pvc <pvc-name>          # ❌ Delete PVC
kubectl delete pvc <pvc-name> -n <namespace>  # ❌ Delete PVC in namespace


# ================================
# 🗂️ STORAGE CLASSES (SC)
# ================================
kubectl get sc                                  # 📋 List all StorageClasses
kubectl describe sc <sc-name>                   # 🔍 Show StorageClass details
kubectl describe storageclass <storage-class-name>  # 🔍 Alternative command


# ================================
# 🧪 DEBUGGING & VALIDATION
# ================================
kubectl exec -it <pod-name> -- df -h   # 💾 Check mounted volumes usage
kubectl exec -it <pod-name> -- ls /mnt # 📂 List files in mounted volume path

# 💡 PRO TIPS
# ============================================
tips:
  - "✅ Always check PVC status (Bound/Pending)"
  - "⚠️ Pending PVC → Check StorageClass & provisioner"
  - "🔐 Use encrypted storage in production"
  - "📍 Use namespace flag (-n) for multi-tenant clusters"
```

# 🎯 Real-World Use Cases

| Scenario | Storage |
|------|------|
| Databases | EBS |
| Shared storage | EFS / NFS |
| Logs | Persistent volumes |
| Config files | ConfigMap |

---

# 🚀 Best Practices

✔ Use **CSI drivers only**  
✔ Prefer **dynamic provisioning**  
✔ Enable **volume expansion**  
✔ Use **WaitForFirstConsumer**  
✔ Choose correct access mode  

---

# 🎯 Quick Summary

| Concept | Meaning |
|------|------|
| StorageClass | Storage template |
| PVC | Storage request |
| PV | Actual storage |
| Provisioner | Backend driver |
| CSI | Modern storage interface |

---

# 💡 Pro Tip

👉 Always design storage based on:

- Performance  
- Scalability  
- Data persistence  
- Cost  

---

# ⭐ Final Thought

StorageClass is a **core concept in Kubernetes storage**.

✔ Enables automation  
✔ Simplifies infrastructure  
✔ Essential for DevOps interviews  

---
