# 🌐 Kubernetes NFS Volume – Complete Guide

> Learn how to use **NFS (Network File System)** in Kubernetes for  
> **shared storage (ReadWriteMany)** across multiple pods.

---

# 📦 What is NFS?

**NFS (Network File System)** allows:

👉 Multiple Pods to **share the same storage over a network**

✔ Works across different nodes  
✔ Supports **ReadWriteMany (RWX)** access  
✔ Ideal for **shared file systems**

---

# 🎯 Why Use NFS?

## 🚀 Key Benefits

✔ Share data between Pods on different nodes  
✔ Enable **simultaneous read/write access**  
✔ Centralized storage management  
✔ Works outside Kubernetes cluster  

---

# 🧠 When to Use NFS?

| Use Case | Example |
|------|------|
| Shared content | Web servers serving same files |
| Logs aggregation | Multiple pods writing logs |
| CI/CD pipelines | Shared workspace |
| Media storage | Images, videos |

---

# 🏗 NFS Architecture

```
        NFS Server
     (External Storage)
             ↓
   PersistentVolume (PV)
             ↓
 PersistentVolumeClaim (PVC)
             ↓
            Pod
             ↓
      Mounted Directory
```

---

# 🔸 Prerequisites

✔ Running **NFS server** with exported directory  
✔ Kubernetes nodes can access NFS (IP/DNS)  
✔ Use **NFSv4** for better performance  

---

# 🧾 1️⃣ PersistentVolume (PV) using NFS

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nfs-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
  - ReadWriteMany
  nfs:
    path: /exported/path
    server: 192.168.1.100
  persistentVolumeReclaimPolicy: Retain
```

---

## 📌 Explanation

| Field | Description |
|------|------|
| storage | Size of volume |
| accessModes | RWX for multi-pod access |
| path | Exported directory on NFS server |
| server | NFS server IP |
| reclaimPolicy | Retain data after PVC deletion |

---

# 🧾 2️⃣ PersistentVolumeClaim (PVC)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nfs-pvc
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
```

---

## 📌 Explanation

✔ Requests storage from PV  
✔ Must match **access mode + size**

---

# 🧾 3️⃣ Pod / Deployment using NFS

```yaml
volumeMounts:
- name: nfs-vol
  mountPath: /usr/share/nginx/html

volumes:
- name: nfs-vol
  persistentVolumeClaim:
    claimName: nfs-pvc
```

---

## 📂 Inside the Container

```
/usr/share/nginx/html  → NFS shared directory
```

✔ All pods using this PVC will see the **same data**

---

# 🔁 Data Sharing Example

```
Pod A (Node 1) ─┐
                ├── Shared NFS Storage
Pod B (Node 2) ─┘
```

✔ Both pods can **read/write simultaneously**

---

# ⚡ Access Modes Explained

| Mode | Description |
|------|------|
| ReadWriteOnce (RWO) | Single node access |
| ReadOnlyMany (ROX) | Multiple read-only |
| ReadWriteMany (RWX) | Multiple read/write |

👉 NFS supports **RWX (multi-node access)**

---

# ⚠️ Important Considerations

❗ Network latency can affect performance  
❗ Not ideal for high IOPS workloads (like databases)  
❗ Requires proper NFS server security  

---

# 🚀 Best Practices

✔ Use **NFSv4**  
✔ Restrict access using firewall rules  
✔ Use **dedicated NFS server**  
✔ Monitor performance and latency  
✔ Avoid for **high-performance databases**

---

# 🎯 Quick Summary

| Feature | Description |
|------|------|
| Type | Network-based storage |
| Access | ReadWriteMany |
| Use Case | Shared storage |
| Scope | Multi-node |
| Backend | External NFS server |

---

# 💡 Pro Tip

👉 Use NFS when you need:

✔ Shared storage across multiple pods  
✔ Simple and cost-effective solution  
✔ Easy setup for development & medium workloads  

---

# ⭐ Final Thought

NFS is one of the **simplest ways to implement shared storage** in Kubernetes.

✔ Great for **RWX workloads**  
✔ Easy to configure  
✔ Widely supported  

---
