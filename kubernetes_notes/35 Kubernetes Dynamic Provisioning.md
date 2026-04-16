# 🚀 Kubernetes Dynamic Provisioning 
## 📦 What is Dynamic Provisioning?

 * **Dynamic Provisioning** allows Kubernetes to:
   * 👉 Automatically create a **PersistentVolume (PV)** when a **PersistentVolumeClaim (PVC)** is created.
   * No need to manually create PVs
   * Fully automated storage lifecycle
   * Automatically create `storage` in Kubernetes using **StorageClass + PVC** (No manual PV creation required ✅)

## 🚀 Key Benefits

  * Eliminates manual PV creation
  * Faster and scalable storage management
  * Uses cloud-native storage (`EBS`, `Azure Disk`, `GCE PD`)
  * Ideal for **production environments**

---

# 🧠 How It Works

```bash
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

## 🔹 Step 1️⃣ StorageClass (EBS CSI Driver)
### 🚀 AWS EBS StorageClass 
```yaml

apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-sc            # 🏷️ Name of the StorageClass

provisioner: ebs.csi.aws.com   # 🔌 CSI Provisioner (AWS EBS)

parameters:
  type: gp3              # 💽 Volume type (gp2 | gp3 | io1 | io2)
  fsType: ext4           # 📁 Filesystem type inside the volume
  encrypted: "true"      # 🔐 Enable encryption using AWS KMS
  iopsPerGB: "10"          # 🚀 IOPS per GB (valid for gp3, io1, io2)  (⚡ Performance tuning (gp3 specific))
  throughput: "125"        # 📊 Throughput in MB/s (gp3 only)

reclaimPolicy: Delete           # ❌ Deletes EBS volume when PVC is deleted  # ♻️ Reclaim policy

volumeBindingMode: WaitForFirstConsumer          # ⏳ Volume binding behavior  (Volume is created only when a Pod uses the PVC (better scheduling))
allowVolumeExpansion: true                       #  📈 Enable resizing    🔄 Allows PVC/EBS volume resize without downtime      
```

---

## 🔍 Important Fields Explained

| 🧩 **Field**                | 📖 **Description**                | 🧠 **How It Works**                                          | 💡 **Real-World Insight**               |
| --------------------------- | --------------------------------- | ------------------------------------------------------------- | --------------------------------------- |
| ⚙️ **provisioner**          | CSI driver used to create volumes | 👉 Defines which storage backend is used (e.g., AWS EBS CSI) | 🔑 Required for dynamic provisioning       |
| 🗄 **type**                 | EBS volume type (e.g., `gp3`)     | 👉 Determines performance (IOPS & throughput)                 | 💰 `gp3` is cost-effective and recommended |
| 🔒 **encrypted**            | Enables encryption using AWS KMS  | 👉 Encrypts data at rest automatically                       | 🛡️ Important for security & compliance     |
| ♻️ **reclaimPolicy**        | What happens after PVC deletion   | 👉 `Delete` → volume removed<br>👉 `Retain` → volume kept    | 🗄 Use `Retain` for critical data          |
| ⏳ **volumeBindingMode**     | Controls when volume is created   | 👉 `WaitForFirstConsumer` → created after Pod scheduling     | 🌍 Prevents wrong AZ placement in AWS      |
| 📏 **allowVolumeExpansion** | Allows resizing of volumes        | 👉 Increase storage size without recreating volume            | 📈 Useful for growing apps/databases      |


---

## 🔹 Step 2️⃣ PersistentVolumeClaim (PVC)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ebs-pvc
spec:
  accessModes:
  - ReadWriteOnce            # Only one node can write at a time
  resources:
    requests:
      storage: 5Gi          # Requesting 5Gi of storage

  storageClassName: ebs-sc   # Refers to the StorageClass above
```

## 📌 What Happens?

  * Kubernetes detects `storageClassName: ebs-sc`
  * Creates a new **EBS volume automatically**
  * Binds it to the PVC  

## 🔹 Step 3️⃣ Deployment Using PVC

```yaml
volumeMounts:
- name: ebs-volume
  mountPath: /usr/share/nginx/html   # EBS volume mounted here

volumes:
- name: ebs-volume
  persistentVolumeClaim:
    claimName: ebs-pvc          # Mounts the PVC to this pod
```

## 📂 Inside Container
```bash
/usr/share/nginx/html → EBS Volume
```

## 🔁 In-Tree vs CSI Driver

 * ❌ 1️⃣ kubernetes.io/aws-ebs (In-tree Plugin)
   * Built into Kubernetes
   * Limited features
      * ❌ No snapshots
      * ❌ Limited resizing
      * ❌ Deprecated approach

## ✅ 2️⃣ ebs.csi.aws.com (CSI Driver)

  * Modern storage interface
  * Fully supported by AWS
  * 🚀 Features :
     * Dynamic provisioning
     * Volume resizing
     * Snapshots support
     * Better logging & tagging
     * High flexibility  
  * 🔐 Security :
     * Uses **IAM Roles for Service Accounts (IRSA)**
     * More secure than `in-tree` plugin  

## ⚠️ Common Mistakes

 * ❌ Forgetting `storageClassName`
 * ❌ Using deprecated in-tree plugin
 * ❌ Not enabling volume expansion
 * ❌ Wrong access mode  

---

## 🚀 Best Practices

 * Always use **CSI drivers (ebs.csi.aws.com)**
 * Use **gp3 for better performance**
 * Enable **encryption**
 * Use **WaitForFirstConsumer**
 * Enable **volume expansion**

## 💡 Pro Tip

 * 👉 In AWS EKS:
    * Use **EBS CSI Driver** for block storage
    * Use **EFS CSI Driver** for shared storage  

---

## ⭐ Final Thought

 * Dynamic provisioning is a **must-know concept** for:
     * Kubernetes admins
     * DevOps engineers
     * CKA / CKAD exams  

