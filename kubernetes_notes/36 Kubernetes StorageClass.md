# 🧱 Kubernetes StorageClass – Complete Guide

> Learn how **StorageClass** enables **dynamic provisioning** in Kubernetes  
> and simplifies storage management 🚀

---

# 📦 What is StorageClass?

A **StorageClass** in Kubernetes defines:

👉 *How storage is dynamically provisioned* when a **PersistentVolumeClaim (PVC)** is created.

✔ Acts like a **template for creating storage**  
✔ Defines performance, type, and behavior  

---

# 🎯 Why Use StorageClass?

## 🚀 Key Benefits

✔ Automatically creates **PersistentVolumes (PVs)**  
✔ Removes manual PV creation  
✔ Ideal for **cloud environments (AWS, GCP, Azure)**  
✔ Simplifies storage management  

---

# 🔧 Why StorageClass is Needed

### ❌ Without StorageClass

- You must manually create PVs  
- Hard to scale  
- Time-consuming  

### ✅ With StorageClass

- Kubernetes auto-creates storage  
- Faster and scalable  
- Fully automated  

---

# 🧠 How It Works

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

---

# ⚙️ Key Components of StorageClass

---

## 🔌 Provisioner

Defines the storage backend

| Provider | Provisioner |
|------|------|
| AWS | ebs.csi.aws.com |
| GCP | pd.csi.storage.gke.io |
| Azure | disk.csi.azure.com |
| NFS | nfs.csi.k8s.io |

---

## ⚙️ Parameters

Define storage configuration

✔ Disk type (gp3, standard, etc.)  
✔ Performance (IOPS, throughput)  
✔ Filesystem (ext4, xfs)  

---

## ♻️ Reclaim Policy

| Policy | Behavior |
|------|------|
| Retain | Keeps data after PVC deletion |
| Delete | Deletes storage automatically |

---

## ⏱ volumeBindingMode

| Mode | Description |
|------|------|
| Immediate | Volume created instantly |
| WaitForFirstConsumer | Created after pod scheduling ✅ |

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

| Provisioner | Description | Platform |
|------|------|------|
| ebs.csi.aws.com | AWS EBS CSI | AWS ✅ |
| pd.csi.storage.gke.io | GCE PD CSI | GCP ✅ |
| disk.csi.azure.com | Azure Disk CSI | Azure ✅ |
| nfs.csi.k8s.io | NFS Storage | Multi-node |
| csi-hostpath | Local testing | Dev only |
| local-storage | Manual local disks | On-prem |

---

# ✅ CSI (Container Storage Interface)

## 🚀 Why CSI?

✔ Supports advanced features  
✔ Dynamic provisioning  
✔ Snapshots & cloning  
✔ Better scalability  

---

## 🔥 Examples

- AWS EBS CSI  
- Azure Disk CSI  
- GCP PD CSI  

---

# 🔁 Static vs Dynamic Provisioning

| Type | Description |
|------|------|
| Static | Admin manually creates PV |
| Dynamic | Kubernetes auto-creates PV |

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
