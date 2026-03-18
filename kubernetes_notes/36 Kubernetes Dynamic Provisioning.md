# 🚀 Kubernetes Dynamic Provisioning – Complete Guide

> Automatically create storage in Kubernetes using **StorageClass + PVC**  
> (No manual PV creation required ✅)

---

# 📦 What is Dynamic Provisioning?

**Dynamic Provisioning** allows Kubernetes to:

👉 Automatically create a **PersistentVolume (PV)**  
when a **PersistentVolumeClaim (PVC)** is created.

✔ No need to manually create PVs  
✔ Fully automated storage lifecycle  

---

# ⚡ Why Use Dynamic Provisioning?

## 🚀 Key Benefits

✔ Eliminates manual PV creation  
✔ Faster and scalable storage management  
✔ Uses cloud-native storage (EBS, Azure Disk, GCE PD)  
✔ Ideal for **production environments**

---

# 🧠 How It Works

```
Step 1: Create PVC
        ↓
Step 2: PVC refers StorageClass
        ↓
Step 3: StorageClass provisions volume
        ↓
Step 4: PV created automatically
        ↓
Step 5: PV bound to PVC
        ↓
Step 6: Pod uses PVC
```

---

# 🔹 Step 1️⃣ StorageClass (EBS CSI Driver)

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-sc

provisioner: ebs.csi.aws.com

parameters:
  type: gp3
  fsType: ext4
  encrypted: "true"
  iopsPerGB: "10"
  throughput: "125"

reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

---

## 🔍 Important Fields Explained

| Field | Description |
|------|------|
| provisioner | CSI driver (EBS in this case) |
| type | EBS volume type (gp3 recommended) |
| encrypted | Enables encryption (AWS KMS) |
| reclaimPolicy | Delete volume after PVC deletion |
| volumeBindingMode | Delays creation until Pod is scheduled |
| allowVolumeExpansion | Enables resizing |

---

# 🔹 Step 2️⃣ PersistentVolumeClaim (PVC)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ebs-pvc

spec:
  accessModes:
  - ReadWriteOnce

  resources:
    requests:
      storage: 5Gi

  storageClassName: ebs-sc
```

---

## 📌 What Happens?

✔ Kubernetes detects `storageClassName: ebs-sc`  
✔ Creates a new **EBS volume automatically**  
✔ Binds it to the PVC  

---

# 🔹 Step 3️⃣ Deployment Using PVC

```yaml
volumeMounts:
- name: ebs-volume
  mountPath: /usr/share/nginx/html

volumes:
- name: ebs-volume
  persistentVolumeClaim:
    claimName: ebs-pvc
```

---

## 📂 Inside Container

```
/usr/share/nginx/html → EBS Volume
```

---

# 🔁 In-Tree vs CSI Driver

---

## ❌ 1️⃣ kubernetes.io/aws-ebs (In-tree Plugin)

- Built into Kubernetes
- Limited features
- ❌ No snapshots
- ❌ Limited resizing
- ❌ Deprecated approach

---

## ✅ 2️⃣ ebs.csi.aws.com (CSI Driver)

- Modern storage interface
- Fully supported by AWS

### 🚀 Features

✔ Dynamic provisioning  
✔ Volume resizing  
✔ Snapshots support  
✔ Better logging & tagging  
✔ High flexibility  

---

## 🔐 Security

✔ Uses **IAM Roles for Service Accounts (IRSA)**  
✔ More secure than in-tree plugin  

---

# 🧠 Key Concepts Summary

| Component | Role |
|------|------|
| StorageClass | Defines storage behavior |
| PVC | Requests storage |
| PV | Created automatically |
| CSI Driver | Provisions storage |

---

# ⚡ Real-World Use Cases

| Scenario | Example |
|------|------|
| Databases | MySQL, PostgreSQL |
| Web apps | Nginx storage |
| Logs | Persistent logs |
| Stateful apps | Kubernetes StatefulSets |

---

# ⚠️ Common Mistakes

❌ Forgetting `storageClassName`  
❌ Using deprecated in-tree plugin  
❌ Not enabling volume expansion  
❌ Wrong access mode  

---

# 🚀 Best Practices

✔ Always use **CSI drivers (ebs.csi.aws.com)**  
✔ Use **gp3 for better performance**  
✔ Enable **encryption**  
✔ Use **WaitForFirstConsumer**  
✔ Enable **volume expansion**

---

# 🎯 Quick Summary

| Feature | Description |
|------|------|
| Dynamic Provisioning | Auto-create PV |
| StorageClass | Defines storage type |
| PVC | Requests storage |
| EBS CSI | Recommended driver |
| ReclaimPolicy | Controls cleanup |

---

# 💡 Pro Tip

👉 In AWS EKS:

- Use **EBS CSI Driver** for block storage  
- Use **EFS CSI Driver** for shared storage  

---

# ⭐ Final Thought

Dynamic provisioning is a **must-know concept** for:

✔ Kubernetes admins  
✔ DevOps engineers  
✔ CKA / CKAD exams  

---

💡 *Add this guide to your GitHub or LinkedIn portfolio to showcase your expertise!*
