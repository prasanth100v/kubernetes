# 🌈 etcdctl – (Backup, Security & Commands)
## 🧾 What is etcdctl?
**etcdctl** is a **command-line tool** to interact with etcd database.

💬 Think:
 - 👉 “Hey etcd, save / read / backup data”
 - 🎯 etcdctl = CLI tool to manage and interact with etcd

### 🎉 Simple Analogy
  - etcd = 🧠 Brain
  - etcdctl = 💬 Voice to talk to the brain

### 🛠️ What Can You Do with etcdctl?
- 📦 Backup & Restore etcd  
- ➕ Add / Remove cluster members  
- 🔍 Get / Put / Delete keys  
- ❤️ Check cluster health  
- 🛠 Troubleshoot issues  

## 🔐 TLS Security in etcdctl
### 🤔 Why TLS is Required?
etcd is secure by default 🔒
  - 👉 Only trusted users can access it

🎯 TLS ensures encrypted and authenticated communication with etcd

### 🔹 1. --cacert (CA Certificate)
- Verifies etcd server  
- Trusts the Certificate Authority  

📁 Path:
```
/etc/kubernetes/pki/etcd/ca.crt
```

### 🔹 2. --cert (Client Certificate)
- Your identity proof  (Client identity)

📁 Path:
```
/etc/kubernetes/pki/etcd/server.crt
```

### 🔹 3. --key (Private Key)
- Confirms you own the certificate  (Proof of identity)

📁 Path:
```
/etc/kubernetes/pki/etcd/server.key
```
### 📁 File Locations (kubeadm)
```
/etc/kubernetes/pki/etcd/
├── ca.crt
├── server.crt
└── server.key
```

## 🔐 Why These Are Required?
  - ✔ Secure communication
  - ✔ Authentication
  - ✔ Prevent unauthorized access  

### 🔄 🔄 Secure Backup Command 
```bash
ETCDCTL_API=3 etcdctl snapshot save /backup/etcd-snapshot.db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key
```
### 🧠 What This Means
 - ✅ “I trust your CA”
 - ✅ “Here is my identity”
 - ✅ “Let’s communicate securely”

### ⚙️ Important Setup
```bash
export ETCDCTL_API=3
```
👉 Always use API version 3

## 🔍 Cluster Inspection Commands
### 📊 Check Cluster Health
```bash
ETCDCTL_API=3 etcdctl \
--endpoints=https://127.0.0.1:2379 \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
endpoint health
```
✅ Output
```
https://127.0.0.1:2379 is healthy
```

### 📋 List Cluster Members
```bash
etcdctl --write-out=table member list
```
## 📦 Check Snapshot Status
```bash
etcdctl --write-out=table snapshot status backup.db
```

---

## 💾 Backup & Restore (Step-by-Step)
### 📦 Take Backup
```bash
etcdctl snapshot save backup.db
```
### 🔄 Restore Backup

```bash
etcdctl snapshot restore backup.db --data-dir=/var/lib/etcd-restored
```
### 🔍 Verify Snapshot
```
etcdctl snapshot status backup.db
```
---

## 🚨🔥 Full Restore Scenario (kubeadm Cluster)
### 📌 Situation You have snapshot:
```
/backup/etcd-snapshot.db
```

### 1️⃣ Stop Services
```bash
sudo systemctl stop kubelet
systemctl stop etcd
```
### 2️⃣ Backup Old Data
```bash
sudo mv /var/lib/etcd /var/lib/etcd.bak
```
### 3️⃣ Restore Snapshot
```bash
ETCDCTL_API=3 etcdctl snapshot restore /backup/etcd-snapshot.db --data-dir /var/lib/etcd
```
### 4️⃣ Fix Permissions
```bash
sudo chown -R etcd:etcd /var/lib/etcd
```
### 5️⃣ Start Services
```bash
sudo systemctl start kubelet
```
 ✔ etcd auto-starts (static pod)

### 6️⃣ Verify Health
```bash
etcdctl endpoint health
```
### ⚠️ Important Checklist
```
Stop kubelet 🛑
Backup old data 📦
Restore snapshot 🔄
Fix permissions 🔐
Start kubelet ▶️
Verify health ✅
```
---

# 🤖 GitOps vs etcd Backup
## ✅ GitOps (ArgoCD / Flux)
### 🟢 GitOps Advantage

With tools like Argo CD / Flux:
```
Git 📘 → Source of truth
Cluster 🔄 → Recreated from Git
```
- ✔ No need to backup workloads
- Recreate cluster easily  

### ❗ But etcd Still Matters
Even with GitOps, etcd stores:
- 🔐 Secrets
- 📦 ConfigMaps
- 💾 PVC metadata
- 🔑 Tokens & certs
- Cluster state 

## ☁️ EKS vs kubeadm
### ❌ EKS
- No etcd access
- AWS manages: HA and Backups

### ✅ kubeadm
- Full control
- Manual backup required

## 🔄 Best Strategy : 🎯 Production Setup
```
etcd Snapshot 📦 (Control Plane)
       +
Velero Backup 📦 (Workloads + PVCs)
       ↓
Complete Disaster Recovery ✅
```

---

## ⚖️ Summary Table
| Option | Meaning | Path |
|-------|--------|------|
| --cacert | Trust server | ca.crt |
| --cert | Identity | server.crt |
| --key | Authentication | server.key |

### ⚠️ Common Mistakes
- ❌ Forgetting TLS flags
- ❌ Not setting ETCDCTL_API=3
- ❌ Not backing up regularly
- ❌ Losing cert files

### 🎯 Key Takeaways
- ✔ Use ETCDCTL_API=3  
- ✔ Always use TLS flags  
- ✔ Snapshots = Disaster recovery  
- ✔ Never edit etcd manually  

### 🎯 Final Summary
```
etcd 🧠
   ↓
etcdctl 💬 (communicate)
   ↓
TLS 🔐 (secure access)
   ↓
Snapshot 📦 (backup)
   ↓
Restore 🔄 (recovery)
```

## 🎉 🧩 Ultimate One-Line

### etcdctl securely manages etcd using TLS, and snapshots ensure full disaster recovery of Kubernetes clusters.
