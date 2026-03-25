# 🚀 ETCD High Availability & Backup 
## 🔥 What is etcd?
**etcd = Kubernetes brain 🧠**

etcd is the core database of Kubernetes that stores:
 - 📦 Pods, Services, Deployments
 - 🔐 Secrets & ConfigMaps
 - 🔑 RBAC rules
 - 🌐 Cluster state
 - 👉 Without etcd → Kubernetes stops working 🚨

### 🎉 Simple Analogy
 - etcd = 🧠 Brain
 - Kubernetes = 🏃 Body 👉 If brain stops → body stops

## ⚡ How Does etcd Stay Available?
👉 To ensure high availability, etcd runs as a **cluster of multiple nodes.**

## ✅ ETCD High Availability Concepts
### 🔹 1. Cluster with Multiple Members

Example:
Instead of one server:
- Node1 (Leader)
- Node2 (Follower)
- Node3 (Follower)

✔ If one node fails → cluster still works (others continue)

### 🔹 2. Raft Consensus Algorithm
 etcd uses Raft to manage consistency and automatic leader election 
- One leader handles writes  
- Followers replicate data  
- Leader failure → new leader elected automatically 🔄  (No manual action required)

👉 Self-healing system

### 🔹 3. Quorum-Based Writes
📘 Formula:
```
Quorum = (Total Nodes / 2) + 1
```

Example:
- 3 nodes → quorum = 2  
- 5 nodes → quorum = 3  
👉 Minimum majority must be alive

✔ Always use **odd number of nodes (3,5,7)**


## 🔹 4. Auto Failover
🔄 What Happens if Leader Fails?
 - Leader crashes 💥
 - Followers detect failure
 - Election happens 🗳️
 - New leader selected 👑

✔ Cluster continues working, 👉 No manual intervention needed

### 🧠 In Short
```
Cluster 🧩 + Raft 🔄 + Quorum 📊 + Auto-Failover 🔁 = High Availability ✅
```
👉 Ensures High Availability 🚀

# 🔥 What if ALL etcd nodes fail?
- Kubernetes control plane stops ❌  
- 💥 No scheduling  
- 💥 No API access  

👉 Only solution:
- Restore snapshot  
- OR rebuild cluster  

---

## 📁 Where is etcd data stored?
In kubeadm clusters:
```
/var/lib/etcd
```

### 🔐 etcd Certificates (Security)
👉 etcd is secured using certificates

Think:
- cert = ID  
- key = password  

### 🤔 Why Certificates?
etcd is secure by default. 👉 It asks: “Are you authorized to access me?” 🔐

### 🎉 Simple Analogy
```
etcd = Secure room 🚪
cert = ID card 🪪
key = Secret key 🔑
```

---

## 📁 Certificate Files (kubeadm)
| Purpose          | File                                  |
| ---------------- | ------------------------------------- |
| CA (Trust)       | `/etc/kubernetes/pki/etcd/ca.crt`     |
| Certificate (ID) | `/etc/kubernetes/pki/etcd/server.crt` |
| Private Key      | `/etc/kubernetes/pki/etcd/server.key` |

## 🧪 Verify Files
```bash
ls /etc/kubernetes/pki/etcd/
```
Should show: ca.crt, server.crt and server.key

---

### 🔄 Take etcd Backup
```bash
ETCDCTL_API=3 etcdctl snapshot save /backup/etcd-snapshot.db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key
```
- ✔ Secure connection  
- ✔ Full cluster backup 
- ✔ Saves .db file

### 🔄 Restore Command
```
ETCDCTL_API=3 etcdctl snapshot restore /backup/etcd-snapshot.db \
  --data-dir /var/lib/etcd-restore
```

---

## ☁️ ETCD in EKS (Managed Kubernetes)
⚠️ Important Limitation
  - ❌ No direct access to etcd in EKS

- ❌ You Cannot Access : etcd server. Certificates and Data directory
- ✅ AWS Handles : etcd backups, High availability and Recovery

---

# 🎯 Best Practices

- ✅ Use odd number of etcd nodes  
- ✅ Take regular backups  
- ✅ Test restore process  
- ✅ Secure certificates  
- ✅ Monitor etcd health  

---

# 🧠 Final Summary

✔ etcd = Kubernetes brain  
✔ HA via clustering + Raft  
✔ Quorum ensures consistency  
✔ Backup = critical  
✔ EKS → AWS manages etcd  

---

🎉 Master ETCD like a PRO!
