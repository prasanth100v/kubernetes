# 🚀 Kubernetes Dynamic Provisioning 
## 📦 What is Dynamic Provisioning?

 * **Dynamic Provisioning** allows Kubernetes to:
   * 👉 Automatically create a **PersistentVolume (PV)** when a **PersistentVolumeClaim (PVC)** is created.
   * 👉 No need to manually create PVs
   * 👉 Fully automated storage lifecycle
   * 👉 Automatically create `storage` in Kubernetes using **StorageClass + PVC** (No manual PV creation required ✅)

## 🚀 Key Benefits
  * Eliminates manual PV creation
  * Faster and scalable storage management
  * Uses cloud-native storage (`EBS`, `Azure Disk`, `GCE PD`)
  * Ideal for **production environments**

---

# 🧠 How It Works

```yaml
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

## ☸️ Manual PV vs Dynamic Provisioning (EKS)
 * 🚀 Manual PV Creation means the storage administrator first creates an `AWS EBS volume manually`, then creates a PersistentVolume (PV) that maps to that EBS volume. Applications create PVCs to consume the storage.
 * ⚡ In Dynamic Provisioning, a StorageClass and PVC are sufficient, and the EBS CSI Driver automatically creates the EBS volume and PV on demand. 

| 🧩 **Feature**             | 🛠️ **Manual PV (Static Provisioning)** | ⚡ **Dynamic Provisioning**               |
| -------------------------- | --------------------------------------- | ---------------------------------------- |
| 💾 **Create EBS Volume**   | ✅ Manual                                | ❌ Automatic                              |
| 📦 **Create PV**           | ✅ Manual                                | ❌ Automatic                              |
| 📥 **Create PVC**          | ✅ Required                              | ✅ Required                               |
| 📦 **StorageClass**        | ❌ Not Required                          | ✅ Required                               |
| 🔌 **EBS CSI Driver**      | ❌ Not Required (Static PV)              | ✅ Required                               |
| ⚙️ **Provisioning Method** | Admin creates storage first             | Kubernetes creates storage automatically |
| ⏱️ **Operational Effort**  | High                                    | Low                                      |
| 🎯 **Suitable For**        | Existing volumes, migrations            | New volumes on demand                    |
| 🚀 **Scalability**         | Limited                                 | Highly scalable                          |
| ☁️ **EKS Best Practice**   | Rarely used                             | Recommended                              |

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

## 🚀 Kubernetes PV, PVC & AWS StorageClass – Rapid Fire Interview Questions & Answers
| 🔢     | ❓ Question                                      | ✅ Answer                                                              |
| ------ | ----------------------------------------------- | --------------------------------------------------------------------- |
| 📦 1   | What is a Persistent Volume (PV)?               | 💾 A cluster level storage resource that exists independently of Pods.      |
| 📄 2   | What is a Persistent Volume Claim (PVC)?        | 📝 A request for storage by a user/application.                       |
| 🔗 3   | Relationship between PV and PVC?                | 🤝 PVC requests storage, PV provides storage.                         |
| 🎯 4   | Why do we need PV?                              | 💾 To retain data even after Pods are deleted.                        |
| 🚀 5   | Why do we need PVC?                             | 📄 To abstract (hide) storage details from applications.              |
| ⚡ 6    | What is a StorageClass?                         | 🏗️ Defines how storage should be dynamically provisioned.            |
| 🔄 7   | What is Dynamic Provisioning?                   | ⚙️ Automatic creation of PV when PVC is created.                      |
| 🛠️ 8  | What is Static Provisioning?                    | 👨‍💻 Admin manually creates PV before PVC.                           |
| 📦 9   | Which object requests storage?                  | 📄 PVC                                                                |
| 💾 10  | Which object provides storage?                  | 📦 PV                                                                 |
| 🔍 11  | Command to view PVs?                            | `kubectl get pv`                                                      |
| 🔍 12  | Command to view PVCs?                           | `kubectl get pvc`                                                     |
| 🔍 13  | Command to view StorageClasses?                 | `kubectl get sc`                                                      |
| 📖 14  | Describe a PV?                                  | `kubectl describe pv <name>`                                          |
| 📖 15  | Describe a PVC?                                 | `kubectl describe pvc <name>`                                         |
| 📖 16  | Describe a StorageClass?                        | `kubectl describe sc <name>`                                          |
| 🌍 17  | Is PV namespace-scoped?                         | ❌ No, Cluster-wide                                                    |
| 🌍 18  | Is PVC namespace-scoped?                        | ✅ Yes                                                                 |
| 🌍 19  | Is StorageClass namespace-scoped?               | ❌ No, Cluster-wide                                                      |
| 🔄 20  | What happens when PVC is created?               | 🔍 Kubernetes searches for matching PV or provisions one dynamically. |
| 🎯 21  | What binds PV and PVC?                          | 📏 Storage size, access mode, StorageClass.                           |
| 📦 22  | Can one PV be used by multiple PVCs?            | ❌ No                                                                  |
| 📄 23  | Can one PVC be mounted by multiple Pods?        | ✅ Depends on Access Mode.                                             |
| 💡 24  | What are Access Modes?                          | ReadWriteOnce, ReadOnlyMany, ReadWriteMany.                           |
| 🔐 25  | What is ReadWriteOnce (RWO)?                    | 🖥️ Mounted as read-write by one node.                                |
| 👀 26  | What is ReadOnlyMany (ROX)?                     | 👁️ Multiple nodes can read.                                          |
| 🌐 27  | What is ReadWriteMany (RWX)?                    | ✍️ Multiple nodes can read and write.                                 |
| 🗑️ 28 | What is Reclaim Policy?                         | Defines what happens after PVC deletion.                              |
| ♻️ 29  | Reclaim policies?                               | Delete, Retain.                                                       |
| 🗑️ 30 | Delete reclaim policy?                          | Removes PV and underlying storage.                                    |
| 💾 31  | Retain reclaim policy?                          | Keeps storage after PVC deletion.                                     |
| 📏 32  | Can PVC size be expanded?                       | ✅ Yes, if StorageClass supports expansion.                            |
| 🚀 33  | Field enabling expansion?                       | `allowVolumeExpansion: true`                                          |
| ⚙️ 34  | What is Volume Binding Mode?                    | Controls when volume provisioning occurs.                             |
| ⏳ 35   | WaitForFirstConsumer?                           | Volume created only after Pod scheduling.                             |
| ⚡ 36   | Immediate mode?                                 | Volume created immediately after PVC creation.                        |
| 🔄 37  | PVC status Pending means?                       | ⏳ Waiting for matching PV or storage provisioning.                    |
| ✅ 38   | PVC status Bound means?                         | 🎉 PVC successfully attached to PV.                                   |
| 🛑 39  | PVC status Lost means?                          | ❌ Bound PV no longer exists.                                          |
| 🚀 40  | Which component provisions volumes dynamically? | CSI Driver                                                            |

## ☁️ AWS EKS StorageClass & EBS CSI Driver
| 🔢     | ❓ Question                                            | ✅ Answer                                  | 
| ------ | ----------------------------------------------------- | -------------------------------------------- | 
| ☁️ 41  | Default StorageClass in older EKS clusters?           | `gp2`                                        |  
| 🚀 42  | Recommended StorageClass now?                         | `gp3`                                        |  
| 💾 43  | Which AWS service provides block storage?             | Amazon EBS                                   |          
| 🔌 44  | Which CSI Driver is used for EBS?                     | `ebs.csi.aws.com`                            |          
| 📦 45  | Why install EBS CSI Driver?                           | To dynamically create EBS volumes from PVCs. |   
| ⚠️ 46  | Can dynamic provisioning work without EBS CSI Driver? | ❌ No                                         |    
| 🔑 47  | What IAM Policy is required?                          | `AmazonEBSCSIDriverPolicy`                   |     
| 🛡️ 48 | Why create IAM Service Account (IRSA)?                | Secure AWS API access from CSI Pods.         |     
| 🌐 49  | Why associate OIDC provider?                          | Required for IRSA authentication.            |  
| 🚀 50  | Command to check CSI Driver?                          | `kubectl get csidrivers`                     |  
| 👀 51  | Expected CSI Driver name?                             | `ebs.csi.aws.com`                            |   
| 📦 52  | CSI Controller Pod location?                          | `kube-system` namespace                      |   
| 📦 53  | CSI Node Pod location?                                | `kube-system` namespace                      |  
| 🔍 54  | Check EBS CSI Pods?                                   | `kubectl get pods -n kube-system | grep ebs` |
| 💾 55  | Which volume type does EBS provide?                   | Block Storage                                |   
| 🌐 56  | Does EBS support RWX?                                 | ❌ No                                         |   
| 🎯 57  | EBS commonly supports?                                | ✅ ReadWriteOnce (RWO)                        |  
| 📂 58  | AWS service for RWX volumes?                          | Amazon EFS                                   |   
| 🔄 59  | Can EBS volume move between AZs?                      | ❌ No                                         | 
| 📍 60  | EBS volumes are created in?                           | Specific Availability Zone                   |   


## 🎤 Frequently Asked Interview Questions
| ❓ Question                                          | 🎯 Answer                                             |
| --------------------------------------------------- | ----------------------------------------------------- |
| Difference between PV and PVC?                      | 📦 PV provides storage, 📄 PVC requests storage.      |
| Difference between Static and Dynamic Provisioning? | 👨‍💻 Manual PV creation vs ⚙️ Automatic PV creation. |
| Why use StorageClass?                               | 🚀 Automates storage provisioning.                    |
| Why is PVC stuck in Pending?                        | ⚠️ No PV available or CSI Driver missing.             |
| Can Pod directly use PV?                            | ❌ No, it uses PVC.                                    |
| Why EBS CSI Driver in EKS?                          | ☁️ To dynamically create EBS volumes.                 |
| Which AWS storage supports RWX?                     | 📂 Amazon EFS                                         |
| Which AWS storage supports RWO?                     | 💾 Amazon EBS                                         |
| What is WaitForFirstConsumer?                       | ⏳ Delays volume creation until Pod scheduling.        |
| What is the latest recommended EBS type?            | 🚀 gp3                                                |

