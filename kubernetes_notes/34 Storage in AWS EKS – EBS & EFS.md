# ☁️✨ Kubernetes Storage in AWS EKS – EBS & EFS Guide ✨☁️
# 🔐✨ In **Amazon EKS**:

 * Use **Amazon EBS** → Block storage (RWO)
 * Use **Amazon EFS** → NFS-based shared storage (RWX)
 * 👉 EFS works with **CSI Driver** for dynamic provisioning

## 📦✨ Storage Options in EKS
| 💾 **Storage**                   | 🧩 **Type**   | 🔐 **Access Mode**     | 📖 **How It Works**                                                            | 💡 **Best Use Case**                               |
| -------------------------------- | ------------- | ---------------------- | ------------------------------------------------------------------------------ | -------------------------------------------------- |
| 🗄 **EBS (Elastic Block Store)** | Block Storage | 🟢 ReadWriteOnce (RWO) | 👉 Attached to a single EC2 node as a disk<br>👉 High performance, low latency | 🗄 Databases (MySQL, PostgreSQL), single-node apps |
| 🌐 **EFS (Elastic File System)** | Network (NFS) | 🟣 ReadWriteMany (RWX) | 👉 Shared file system accessible by multiple nodes<br>👉 Mounted over network  | 🌍 Shared storage (web content, logs, CI/CD)       |


## ✨ Important Fields Explained
## 📏✨ capacity.storage
Defines volume size
```yaml
storage: 5Gi
```

## 🔁✨ accessModes
| 🔐 Access Mode             | 📖 Description              | 🧠 How It Works                                           | ☁️ Supported Storage             | 💡 Real-World Use Case                 |
| -------------------------- | --------------------------- | --------------------------------------------------------- | -------------------------------- | -------------------------------------- |
| 🟢 **ReadWriteOnce (RWO)** | 📦 One Pod read/write       | 👉 Mounted as read-write on **single node only**          | 💾 AWS EBS, GCP PD, Azure Disk   | 🗄 Databases (MySQL, PostgreSQL)       |
| 🔵 **ReadOnlyMany (ROX)**  | 📚 Multiple Pods read-only  | 👉 Many Pods can read, **no writes allowed**              | ⚠️ Limited (depends on provider) | 📄 Static content (docs, configs)      |
| 🟣 **ReadWriteMany (RWX)** | 🌐 Multiple Pods read/write | 👉 Multiple Pods across nodes read & write simultaneously | 📂 Amazon EFS, NFS, Azure Files  | 🌐 Shared storage (logs, media, CI/CD) |


## ⚠️🚨 Important Note
❗ Not all storage supports all modes  
👉 Example: **EBS supports only RWO**

## ♻️ Kubernetes `persistentVolumeReclaimPolicy`
  Defines what happens after PVC deletion
  
| 🔧 Policy         | 📖 Description                     | 💡 Behavior                                  | 🎯 Best Use Case                             |
| ----------------- | ---------------------------------- | -------------------------------------------- | -------------------------------------------- |
| 🔹 **Retain**     | 💾 Keeps volume after PVC deletion | ✔ Data remains<br>⚠️ Manual cleanup required | 🛡️ Data safety (databases, critical data)   |
| 🔹 **Delete**     | 🗑️ Deletes storage automatically  | ✔ Removes PV + underlying storage            | ⚡ Dynamic provisioning (temporary workloads) |
| 🔹 **Recycle** ⚠️ | 🧹 Clears data, but keeps volume   | ✔ Wipes data<br>⚠️ Deprecated / limited use  | 🚫 Not recommended                           |


---

# 🧾✨ Static Provisioning with AWS EBS
## 🔹📌 Prerequisites
 * Create EBS volume manually (AWS Console / CLI)
 * Note:
    * Volume ID
    * Availability Zone (AZ) (Must match worker node AZ)

## 📦 1️⃣ PersistentVolume (PV)
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

## 🔍✨ storageClassName: manual
  * Indicates **static provisioning**
     * 👉 Kubernetes will NOT create storage dynamically
     * 👉 It uses **existing EBS volume**

## 📄 2️⃣ PersistentVolumeClaim (PVC)
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

## 🔗✨ Binding Flow
```
PVC → Matches PV → Bound → Ready to use
```


## 🚀✨ 3️⃣ Mount PVC to Pod
```yaml
volumeMounts:
- name: ebs-volume
  mountPath: /data

volumes:
- name: ebs-volume
  persistentVolumeClaim:
    claimName: ebs-pvc
```

## 📂✨ Inside Container
```
/data → EBS volume storage
```

# 🧠✨ EBS vs EFS Quick Comparison

| 🧩 **Feature**            | 💾 **EBS (Elastic Block Store)**               | 📂 **EFS (Elastic File System)**                   |
| ------------------------- | ---------------------------------------------- | -------------------------------------------------- |
| 🗂️ **Storage Type**      | 📦 Block Storage                               | 📁 Network File Storage (NFS)                      |
| 🔄 **Access Mode**        | 🟢 RWO (ReadWriteOnce)                         | 🟣 RWX (ReadWriteMany)                             |
| 🖥️ **Multi-Node Access** | ❌ Not Supported                                | ✅ Supported                                        |
| ☸️ **Kubernetes Access**  | One Pod/Node typically writes at a time        | Multiple Pods across multiple nodes can read/write |
| 🎯 **Best Use Case**      | 🗄️ Databases, Stateful Applications, OS Disks | 🌐 Shared Files, Uploads, Logs, Website Content    |
| ⚡ **Performance**         | 🚀 High Performance, (Low Latency)           | ⚖️ Moderate, (Network-Based)                         |
| 💰 **Cost**               | Usually Lower                                  | Usually Higher                                     |
| 🔒 **Availability**       | Single AZ Volume                               | Multi-AZ by Design                                 |
| 📈 **Scalability**        | Manual Resize                                  | Automatically Scales                               |
| ☁️ **EKS CSI Driver**     | EBS CSI Driver                                 | EFS CSI Driver                                     |

## ⚠️ Common Mistakes
 * ❌ Using EBS for `multi-node` workloads
 * ❌ `AZ mismatch` between `PV` and `node`
 * ❌ Forgetting `storageClassName`
 * ❌ Expecting `RWX` from EBS  

---

# 🚀✨ Best Practices
 * Use **EBS for databases**
 * Use **EFS for shared storage**
 * Prefer **dynamic provisioning with CSI drivers**
 * Always `match AZ` for EBS volumes
 * Use **Retain policy for critical data**

## 💡✨ Pro Tip
 * 👉 In production EKS:
   - Use **EBS CSI Driver** for dynamic volumes  
   - Use **EFS CSI Driver** for shared workloads  
