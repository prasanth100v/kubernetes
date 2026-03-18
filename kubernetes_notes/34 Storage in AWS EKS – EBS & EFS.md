# ☁️ Kubernetes Storage in AWS EKS – EBS & EFS Guide

> Learn how to use **AWS EBS & EFS with Kubernetes (EKS)** including  
> access modes, reclaim policies, and PV/PVC examples.

---

# 🔐 In **Amazon EKS**:

✔ Use **Amazon EBS** → Block storage (RWO)  
✔ Use **Amazon EFS** → NFS-based shared storage (RWX)  

👉 EFS works with **CSI Driver** for dynamic provisioning

---

# 📦 Storage Options in EKS

| Storage | Type | Access Mode |
|------|------|------|
| EBS | Block Storage | ReadWriteOnce |
| EFS | Network (NFS) | ReadWriteMany |

---

# 🧠 Important Fields Explained

---

## 📏 capacity.storage

Defines volume size

```yaml
storage: 5Gi
```

---

## 🔁 accessModes

### 🔹 ReadWriteOnce (RWO)

✔ Only one pod can read/write  
✔ Works on a single node  

✅ Supported by:
- AWS EBS
- GCP Persistent Disk
- Azure Disk

---

### 🔹 ReadOnlyMany (ROX)

✔ Multiple pods can read  
❌ No write access  

---

### 🔹 ReadWriteMany (RWX)

✔ Multiple pods can read/write  

✅ Supported by:
- Amazon EFS
- NFS
- Azure Files

---

## ⚠️ Important Note

❗ Not all storage supports all modes  
👉 Example: **EBS supports only RWO**

---

# ♻️ persistentVolumeReclaimPolicy

Defines what happens after PVC deletion

---

## 🔹 Retain

✔ Keeps data after PVC deletion  
✔ Manual cleanup required  

👉 Best for **data safety**

---

## 🔹 Delete

✔ Deletes underlying storage automatically  

👉 Used in **dynamic provisioning**

---

## 🔹 Recycle (Deprecated / Limited Use)

✔ Clears data but keeps volume  

---

# 🧾 Static Provisioning with AWS EBS

---

# 🔹 Prerequisites

✔ Create EBS volume manually (AWS Console / CLI)  
✔ Note:
- Volume ID
- Availability Zone (AZ)

✔ Must match worker node AZ  

---

# 📦 1️⃣ PersistentVolume (PV)

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: ebs-pv
spec:
  capacity:
    storage: 5Gi
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual

  awsElasticBlockStore:
    volumeID: vol-0abcd1234efgh5678
    fsType: ext4
```

---

## 🔍 storageClassName: manual

✔ Indicates **static provisioning**

👉 Kubernetes will NOT create storage dynamically  
👉 It uses **existing EBS volume**

---

# 📄 2️⃣ PersistentVolumeClaim (PVC)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ebs-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: manual
  resources:
    requests:
      storage: 5Gi
```

---

## 🔗 Binding Flow

```
PVC → Matches PV → Bound → Ready to use
```

---

# 🚀 3️⃣ Mount PVC to Pod

```yaml
volumeMounts:
- name: ebs-volume
  mountPath: /data

volumes:
- name: ebs-volume
  persistentVolumeClaim:
    claimName: ebs-pvc
```

---

## 📂 Inside Container

```
/data → EBS volume storage
```

---

# 🧠 EBS vs EFS Quick Comparison

| Feature | EBS | EFS |
|------|------|------|
| Type | Block | NFS |
| Access | RWO | RWX |
| Multi-node | ❌ | ✔ |
| Use Case | Databases | Shared storage |
| Performance | High | Moderate |

---

# ⚡ Real-World Use Cases

| Scenario | Storage |
|------|------|
| MySQL / PostgreSQL | EBS |
| Shared uploads | EFS |
| Logs sharing | EFS |
| Stateful apps | EBS |

---

# ⚠️ Common Mistakes

❌ Using EBS for multi-node workloads  
❌ AZ mismatch between PV and node  
❌ Forgetting storageClassName  
❌ Expecting RWX from EBS  

---

# 🚀 Best Practices

✔ Use **EBS for databases**  
✔ Use **EFS for shared storage**  
✔ Prefer **dynamic provisioning with CSI drivers**  
✔ Always match AZ for EBS volumes  
✔ Use **Retain policy for critical data**

---

# 🎯 Quick Summary

| Concept | Meaning |
|------|------|
| PV | Actual storage |
| PVC | Storage request |
| EBS | Block storage (RWO) |
| EFS | Shared storage (RWX) |
| Retain | Keeps data |
| Delete | Removes storage |

---

# 💡 Pro Tip

👉 In production EKS:

- Use **EBS CSI Driver** for dynamic volumes  
- Use **EFS CSI Driver** for shared workloads  

---

# ⭐ Final Thought

Understanding storage in Kubernetes (especially in EKS) is **critical for DevOps interviews**.

✔ Helps design **stateful applications**  
✔ Ensures **data safety**  
✔ Improves **system scalability**

---
