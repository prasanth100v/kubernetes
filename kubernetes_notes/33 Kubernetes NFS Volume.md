# 🌐 Kubernetes NFS Volume 
## 📦 What is NFS?

 * **NFS (Network File System)** allows Multiple Pods to **share the same storage over a network**
    * Works across different nodes
    * Supports **ReadWriteMany (RWX)** access
    * Ideal for **shared file systems**

## 🚀 Key Benefits

  * Share data between Pods on different nodes
  * Enable **simultaneous read/write access**
  * Centralized storage management
  * Works outside Kubernetes cluster  

## 🏗 NFS Architecture

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

## 🔸 Prerequisites

  * Running **NFS server** with exported directory
  * Kubernetes nodes can access NFS (`IP/DNS`)
  * Use **NFSv4** for better performance  

## 🧾 1️⃣ PersistentVolume (PV) using NFS

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

| 🧩 Field           | 💡 Description                              |
| ------------------ | -------------------------------------------- |
| 💾 `storage`       | 📏 Defines size of the volume               |
| 🔄 `accessModes`   | 📂 `RWX` → Multiple Pods can read/write     |
| 📁 `path`          | 🗂️ Directory exported from NFS server       |
| 🌐 `server`        | 📡 NFS server IP address                    |
| ♻️ `reclaimPolicy` | 🛡️ `Retain` → data after PVC deletion       |


## 🧾 2️⃣ PersistentVolumeClaim (PVC)

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

## 📌 Explanation

* Requests storage from PV  
* Must match **access mode + size**

## 🧾 3️⃣ Pod / Deployment using NFS

```yaml
volumeMounts:
- name: nfs-vol
  mountPath: /usr/share/nginx/html

volumes:
- name: nfs-vol
  persistentVolumeClaim:
    claimName: nfs-pvc
```

## 📂 Inside the Container
```
/usr/share/nginx/html  → NFS shared directory
```
* All pods using this PVC will see the **same data**

## 🔁 Data Sharing Example
```
Pod A (Node 1) ─┐
                ├── Shared NFS Storage
Pod B (Node 2) ─┘
```
* Both pods can **read/write simultaneously**

---

# ⚡ Access Modes Explained

| 🔐 Mode                    | 📖 Description               | 🧠 How It Works                                         | 💡 Real-World Use Case                   |
| -------------------------- | ---------------------------- | ------------------------------------------------------- | ---------------------------------------- |
| 🟢 **ReadWriteOnce (RWO)** | 📦 Single node read/write    | 👉 Mounted as read-write by **only one node at a time** | 🗄 Databases (MySQL, PostgreSQL)         |
| 🔵 **ReadOnlyMany (ROX)**  | 📚 Multiple nodes read-only  | 👉 Many nodes can read, but **no writes allowed**       | 📄 Shared static content (docs, configs) |
| 🟣 **ReadWriteMany (RWX)** | 🌐 Multiple nodes read/write | 👉 Multiple nodes can read & write simultaneously       | 📂 Shared storage (NFS, EFS)             |

👉 NFS supports **RWX (multi-node access)**

## ⚠️ Important Considerations

 * ❗ Network latency can affect performance  
 * ❗ Not ideal for high IOPS workloads (like databases)
 * ❗ Requires proper NFS server security  

## 🚀 Best Practices

  * Use **NFSv4**
  * Restrict access using firewall rules
  * Use **dedicated NFS server**
  * Monitor performance and latency
  * Avoid for **high-performance databases**

# 💡 Pro Tip

 * 👉 Use NFS when you need:
     * Shared storage across multiple pods
     * Simple and cost-effective solution
     * Easy setup for development & medium workloads  

---

## ⭐ Final Thought

 * NFS is one of the **simplest ways to implement shared storage** in Kubernetes.
    * Great for **RWX workloads**
    * Easy to configure
    * Widely supported  
