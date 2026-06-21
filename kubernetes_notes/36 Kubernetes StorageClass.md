# 🧱 Kubernetes StorageClass 
# 📦 What is StorageClass?
 * A **StorageClass** in Kubernetes defines:
     * 👉 *How storage is dynamically provisioned* when a **PersistentVolumeClaim (PVC)** is created.
     * 👉 Persistent Volumes (`PVs`) are dynamically created.
     * 👉 Acts like a **template for creating storage**
     * 👉 Defines `performance`, `type`, and `behavior ` 

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
```yaml
PVC Created
     ↓
StorageClass Referenced
     ↓
Provisioner Creates Volume
     ↓
StorageClass automatically creates PV
     ↓
Bound to PVC
     ↓
Used by Pod
```

## ⚙️ Key Components of StorageClass
### 🔌 Kubernetes CSI Provisioners (Cloud Providers - Defines the storage backend)
| 🌍 **Cloud Provider / Storage** | ⚙️ **CSI Provisioner**   | 📖 **Storage Type**      | 🎯 **Common Use Case**           |
| ------------------------------- | ------------------------ | ------------------------ | -------------------------------- |
| ☁️ **AWS EBS**                  | `ebs.csi.aws.com`        | Block Storage            | MySQL, PostgreSQL, StatefulSets  |
| 📂 **AWS EFS**                  | `efs.csi.aws.com`        | Shared File Storage      | Shared files, uploads, logs      |
| 🌐 **GCP Persistent Disk**      | `pd.csi.storage.gke.io`  | Block Storage            | Databases, Stateful Applications |
| 🔷 **Azure Disk**               | `disk.csi.azure.com`     | Block Storage            | Databases and Stateful Workloads |
| 📁 **Azure Files**              | `file.csi.azure.com`     | Shared File Storage      | Multi-pod shared storage         |
| 📂 **NFS**                      | `nfs.csi.k8s.io`         | Network File Storage     | Shared storage across nodes      |

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

 * 👉 **Recommended: WaitForFirstConsumer**

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


## ⚠️ Important Notes
 * ✔ Only **PersistentVolumes (PV)** preserve data
 * ❌ emptyDir, hostPath → Not persistent  

### You can mount multiple volumes:
```yaml
volumeMounts:
- name: config
  mountPath: /config

- name: logs
  mountPath: /logs
```

## ☁️ AWS EBS: Old vs New
| 🧩 **Feature**           | ❌ **kubernetes.io/aws-ebs (In-tree)** | ✅ **ebs.csi.aws.com (CSI Driver)**                |
| ------------------------ | ------------------------------------- | ------------------------------------------------- |
| 📖 **Type**              | Built into Kubernetes core            | External CSI (Container Storage Interface) driver |
| ⚙️ **Status**            | 🚫 Deprecated                         | ✅ Actively maintained & recommended               |
| 🚀 **Features**          | ❌ Limited capabilities                | ✔ Supports resizing, snapshots, volume expansion  |
| 🔐 **Security**          | ⚠️ Basic IAM usage                    | ✔ Uses IAM Roles for Service Accounts (IRSA)      |
| 🔄 **Flexibility**       | ❌ Less flexible                       | ✔ Highly flexible & extensible                    |
| ☁️ **Cloud Integration** | ⚠️ Tight coupling with Kubernetes     | ✔ Better integration with AWS services            |
| 🔧 **Future Support**    | ❌ Being removed                       | ✅ Future-proof                                    |

### ⚠️ Important in EKS
 * 👉 CSI driver is **NOT installed by default**  
 * 👉 You must install it manually  

---

# 🧾 Useful kubectl Commands
## 📦 Kubernetes Storage Commands Cheat Sheet
```
kubectl get pv                             # 📋 List all PersistentVolumes
kubectl describe pv <pv-name>              # 🔍 Show detailed PV info
kubectl delete pv <pv-name>                # ❌ Delete a PersistentVolume

kubectl get pvc                                # 📋 List all PVCs
kubectl get pvc -n <namespace>                 # 📂 PVCs in specific namespace
kubectl describe pvc <pvc-name>                # 🔍 Show PVC details
kubectl apply -f pvc.yaml                      # ⚙️ Create PVC using YAML
kubectl delete pvc <pvc-name>                  # ❌ Delete PVC
kubectl delete pvc <pvc-name> -n <namespace>   # ❌ Delete PVC in namespace
=
kubectl get sc                                          # 📋 List all StorageClasses
kubectl describe sc <sc-name>                           # 🔍 Show StorageClass details
kubectl describe storageclass <storage-class-name>      # 🔍 Alternative command

kubectl exec -it <pod-name> -- df -h          # 💾 Check mounted volumes usage
kubectl exec -it <pod-name> -- ls /mnt       # 📂 List files in mounted volume path


# 💡 PRO TIPS
tips:
  - "✅ Always check PVC status (Bound/Pending)"
  - "⚠️ Pending PVC → Check StorageClass & provisioner"
  - "🔐 Use encrypted storage in production"
  - "📍 Use namespace flag (-n) for multi-tenant clusters"
```

# 🚀 Best Practices
 * Use **CSI drivers only**
 * Prefer **dynamic provisioning**
 * Enable **volume expansion**
 * Use **WaitForFirstConsumer**
 * Choose correct access mode  

---

## 🎯 Quick Summary
| 🧩 **Concept**                           | 💡 **Meaning**      | 📖 **Description**                                                                 | 🎯 **Example**                 |
| ---------------------------------------- | ------------------- | ---------------------------------------------------------------------------------- | ------------------------------ |
| 📦 **StorageClass (SC)**                 | 🧾 Storage Template | Defines how storage should be dynamically created (type, provisioner, parameters)  | `gp3`, `efs-sc`, `nfs-storage` |
| 📥 **PersistentVolumeClaim (PVC)**       | 🙋 Storage Request  | Request made by a Pod/Application for storage                                      | Request 10Gi storage           |
| 💾 **PersistentVolume (PV)**             | 📂 Actual Storage   | Real storage provisioned and bound to a PVC                                        | EBS Volume, EFS File System    |
| ⚙️ **Provisioner**                       | 🔧 Storage Creator  | Driver responsible for creating storage resources                                  | `ebs.csi.aws.com`              |
| 🔌 **CSI (Container Storage Interface)** | 🚀 Storage Standard | Standard interface that allows Kubernetes to work with different storage providers | AWS EBS CSI Driver             |

## 💡 Pro Tip
👉 Always design storage based on:
   - Performance  
   - Scalability  
   - Data persistence  
   - Cost  

---



