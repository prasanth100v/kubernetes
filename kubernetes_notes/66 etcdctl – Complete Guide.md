# 🌈 etcdctl – (Backup, Security & Commands)
## 🧾 What is etcdctl?
 * **etcdctl** is a **command-line tool** to interact with etcd database.
 * 💬 Think:
    - 👉 “Hey etcd, save / read / backup data”
    - 🎯 etcdctl = `CLI tool` to manage and `interact` with etcd

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
  * etcd is secure by default 🔒
    - 👉 Only trusted users can access it
    - 🎯 TLS ensures `encrypted` and `authenticated communication` with etcd

### 🔹 1. --cacert (CA Certificate)
 - Verifies etcd server  
 - Trusts the Certificate Authority
 - 📁 Path:
```hcl
/etc/kubernetes/pki/etcd/ca.crt
```

### 🔹 2. --cert (Client Certificate)
 - Your identity proof  (Client identity)
 - 📁 Path:
```hcl
/etc/kubernetes/pki/etcd/server.crt
```

### 🔹 3. --key (Private Key)
  - Confirms you own the certificate  (Proof of identity)
  - 📁 Path:
```yaml
/etc/kubernetes/pki/etcd/server.key
```

### 📁 File Locations (kubeadm)
```hcl
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
```hcl
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
```yaml
export ETCDCTL_API=3
```
 * 👉 Always use API version 3

## 🔍 Cluster Inspection Commands
### 📊 Check Cluster Health
```hcl
ETCDCTL_API=3 etcdctl \
--endpoints=https://127.0.0.1:2379 \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
endpoint health
```
✅ Output
```yaml
https://127.0.0.1:2379 is healthy
```

### 📋 List Cluster Members
```yaml
etcdctl --write-out=table member list
```
### Output:
```hcl
+------------------+------------+----------+---------------------------+-----------------------------+
| ID               | STATUS     | NAME     | PEER ADDRS                | CLIENT ADDRS                |
+------------------+------------+----------+---------------------------+-----------------------------+
| 1234abcd...      | started    | node1    | https://10.0.0.1:2380     | https://10.0.0.1:2379       |
+------------------+------------+----------+---------------------------+-----------------------------+
```

## 📦 Check Snapshot Status
```hcl
etcdctl --write-out=table snapshot status /tmp/etcd-snapshot.db
```
### Output:
```yaml
+----------+-------------+------------------+-----------------+
| HASH     | REVISION    | TOTAL KEYS       | TOTAL SIZE      |
+----------+-------------+------------------+-----------------+
| fe4cf... | 12345       | 1428             | 25 MB           |
+----------+-------------+------------------+-----------------+
```

---

## 💾 Backup & Restore (Step-by-Step)
```hcl
etcdctl snapshot save backup.db                                              # 📦 Take Backup
etcdctl snapshot restore backup.db --data-dir=/var/lib/etcd-restored         # 🔄 Restore Backup
etcdctl snapshot status backup.db                                            # 🔍 Verify Snapshot
```
---

## 🚨🔥 Full Restore Scenario (kubeadm Cluster)
### 📌 Situation You have snapshot:
```yaml
/backup/etcd-snapshot.db
```

### 1️⃣ Stop Services
```yaml
sudo systemctl stop kubelet
systemctl stop etcd
```
### 2️⃣ Backup Old Data
```yaml
sudo mv /var/lib/etcd /var/lib/etcd.bak
```
### 3️⃣ Restore Snapshot
```hcl
ETCDCTL_API=3 etcdctl snapshot restore /backup/etcd-snapshot.db --data-dir /var/lib/etcd
```
### 4️⃣ Fix Permissions
```yaml
sudo chown -R etcd:etcd /var/lib/etcd
```
### 5️⃣ Start Services
```yaml
sudo systemctl start kubelet
```
 * ✔ etcd auto-starts (static pod)

### 6️⃣ Verify Health
```yaml
etcdctl endpoint health
```
### ⚠️ Important Checklist

| 🔢 **Step** | 📖 **Action**           | 🧠 **Why It Matters**                                 | 💡 **Pro Tip**                 |
| ----------- | ----------------------- | ------------------------------------------------------- | ------------------------------ |
| 1️⃣         | 🛑 **Stop kubelet**     | 👉 Prevents Kubernetes from interfering during restore | `systemctl stop kubelet`       |
| 2️⃣         | 📦 **Backup old data**  | 👉 Safety fallback if restore fails                    | Copy `/var/lib/etcd`           |
| 3️⃣         | 🔄 **Restore snapshot** | 👉 Rebuild etcd from backup                            | Use `etcdctl snapshot restore` |
| 4️⃣         | 🔐 **Fix permissions**  | 👉 Ensure etcd can read/write data                     | `chown -R etcd:etcd`           |
| 5️⃣         | ▶️ **Start kubelet**    | 👉 Bring control plane back online                     | `systemctl start kubelet`      |
| 6️⃣         | ✅ **Verify health**     | 👉 Confirm cluster is working                         | `etcdctl endpoint health`      |

---

# 🤖 GitOps vs etcd Backup
## ✅ GitOps (ArgoCD / Flux)
### 🟢 GitOps Advantage

 * With tools like Argo CD / Flux:
```yaml
Git 📘       → Source of truth
Cluster 🔄   → Recreated from Git
```
  - ✔ No need to backup workloads
  - Recreate cluster easily  

### ❗ But etcd Still Matters
 * Even with GitOps, etcd stores:
    - 🔐 Secrets
    - 📦 ConfigMaps
    - 💾 PVC metadata
    - 🔑 Tokens & certs
    - Cluster state 

## ☁️ EKS vs kubeadm

| 🧩 **Feature**           | ☁️ Amazon Elastic Kubernetes Service (EKS)                    | 🛠 **kubeadm (Self-managed)**                   |
| ------------------------ | -------------------------------------------------------------- | ------------------------------------------------ | 
| 🔓 **etcd Access**       | ❌ Not accessible<br>Less control<br>EKS hides control plane | ✅ Full access<br>full control                |
| 💾 **Backup**            | ✅ AWS managed<br>AWS handles snapshots                      | ❌ Manual (etcdctl)<br>you must manage           | 
| 🛡 **High Availability**  | ✅ Managed by AWS<br>EKS provides HA control plane<br>Reduced complexity  | ⚠️ You configure                    |  
| 🔐 **Certificates**      | ❌ Not accessible<br>AWS manages PKI                          | ✅ Available<br>user-managed Certificates        |
| 🧑‍💻 **Responsibility**    | AWS Managed                                                   | You self-managed                                 |
| ⚙️ **Control**           | Limited<br>Less flexibility                                   | Full<br>full customization                        |


## ☸️ etcd in Kubernetes & Amazon EKS
| 🧩 **Cluster Type**                    |                                                    💾 **etcd Members** | 👑 **Leader Election** | 🗳️ **Raft Consensus** | ✅ **High Availability**   |
| -------------------------------------- | ---------------------------------------------------------------------: | :--------------------: | :--------------------: | :-------------------------: |
| 🖥️ **Single Control Plane (kubeadm)** |                                                                      1 |            ❌           |            ❌           |            ❌            |
| 🏗️ **Multi Control Plane (kubeadm)**  |                                                                 3 or 5 |            ✅           |            ✅           |            ✅            |
| ☁️ **Amazon EKS**                      | AWS-managed (multiple etcd members across multiple Availability Zones) |            ✅           |            ✅           |            ✅           |

 ### Key interview point
  * etcd always belongs to the `Control Plane`, never the worker nodes.
  * In a single-control-plane kubeadm cluster, there is only `one etcd member`, so there is `no leader election` or `Raft replication`.
  * In a highly available kubeadm cluster (`multiple control planes`) and in Amazon EKS, etcd runs as a `distributed cluster` that uses the `Raft consensus algorithm` for leader election and `data replication`
  * Providing high availability and consistency.

---

## ⚖️ Summary Table
 * In etcdctl, `--cacert` is used to verify the server, `--cert` provides client identity, and `--key` is used for `authentication`, enabling secure TLS communication with etcd.

| 🧩 **Option** | 📖 **Meaning** | 🧠 **What It Does**                 | 📁 **File Path (kubeadm)**            |
| ------------- | -------------- | ----------------------------------- | ------------------------------------- |
| 🔒 `--cacert` | Trust server   | 👉 Verifies etcd server using CA    | `/etc/kubernetes/pki/etcd/ca.crt`     |
| 🆔 `--cert`   | Identity       | 👉 Client certificate (who you are) | `/etc/kubernetes/pki/etcd/server.crt` |
| 🔑 `--key`    | Authentication | 👉 Private key for secure auth      | `/etc/kubernetes/pki/etcd/server.key` |

### 🎯 Key Takeaways
  - ✔ Use ETCDCTL_API=3  
  - ✔ Always use TLS flags  
  - ✔ Snapshots = Disaster recovery  
  - ✔ Never edit etcd manually  

### 🎯 Final Summary
```yaml
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

* etcdctl securely manages etcd using `TLS`, and snapshots ensure `full disaster recovery` of Kubernetes clusters.

## 🚀 Kubernetes etcd – Rapid Fire Interview Questions & Answers
| 🔢     | ❓ Interview Question                                        | ✅ Answer                                                                                                                                                                                                                                |
| ------ | ----------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1️⃣    | What is **etcd**?                                           | 💾 A distributed, highly available **key-value database** used by Kubernetes to store cluster data.                                                                                                                                     |
| 2️⃣    | Why does Kubernetes use etcd?                               | 📦 To store the `entire cluster state` and `configuration`.                                                                                                                                                                                 |
| 3️⃣    | What type of database is etcd?                              | 🔑 Distributed Key-Value Store.                                                                                                                                                                                                         |
| 4️⃣    | Who developed etcd?                                         | 🚀 The **CoreOS** team (now part of **Red Hat**).                                                                                                                                                                                       |
| 5️⃣    | Which Kubernetes component communicates directly with etcd? | 🌐 **kube-apiserver**                                                                                                                                                                                                                   |
| 9️⃣    | Which component acts as a bridge to etcd?                   | 🌐 kube-apiserver                                                                                                                                                                                                                       |
| 🔟     | Where is etcd installed in kubeadm clusters?                | ☸️ `On the Control Plane node. `                                                                                                                                                                                                          |
| 1️⃣1️⃣ | Who manages etcd in Amazon EKS?                             | ☁️ AWS manages etcd as part of the managed control plane.                                                                                                                                                                               |
| 1️⃣2️⃣ | Can users access etcd directly in EKS?                      | ❌ No                                                                                                                                                                                                                                    |
| 1️⃣3️⃣ | What information is stored in etcd?                         | 📦 Pods, Deployments, Services, Secrets, ConfigMaps, Nodes, Namespaces, CRDs, RBAC, and more.                                                                                                                                           |
| 1️⃣4️⃣ | Are Secrets stored in etcd?                                 | ✅ Yes                                                                                                                                                                                                                                   |
| 1️⃣5️⃣ | Are ConfigMaps stored in etcd?                              | ✅ Yes                                                                                                                                                                                                                                   |
| 1️⃣6️⃣ | Are CRDs stored in etcd?                                    | ✅ Yes                                                                                                                                                                                                                                   |
| 1️⃣7️⃣ | Are PersistentVolume objects stored in etcd?                | ✅ Yes (the object metadata (data about data.), not the actual storage data).                                                                                                                                                                               |
| 1️⃣8️⃣ | Does etcd store container images?                           | ❌ No                                                                                                                                                                                                                                    |
| 1️⃣9️⃣ | Does etcd store application logs?                           | ❌ No                                                                                                                                                                                                                                    |
| 2️⃣0️⃣ | Does etcd store actual application data?                    | ❌ No, only Kubernetes object state and metadata.                                                                                                                                                                                        |
| 2️⃣1️⃣ | How does Kubernetes ensure etcd consistency?                | 🤝 Using the **Raft consensus algorithm**.                                                                                                                                                                                              |
| 2️⃣2️⃣ | What is the purpose of Raft?                                | 🔄 Keeps all etcd members consistent.                                                                                                                                                                                                   |
| 2️⃣3️⃣ | What is an etcd Leader?                                     | 👑 Member that handles all write operations.                                                                                                                                                                                            |
| 2️⃣4️⃣ | What are Followers?                                         | 👥 Members that replicate data from the leader.                                                                                                                                                                                         |
| 2️⃣5️⃣ | How many Leaders exist in one etcd cluster?                 | 👑 Only one                                                                                                                                                                                                                             |
| 2️⃣6️⃣ | Can Followers accept writes?                                | ❌ No                                                                                                                                                                                                                                    |
| 2️⃣7️⃣ | Who replicates writes to Followers?                         | 👑 Leader                                                                                                                                                                                                                               |
| 2️⃣8️⃣ | What happens if the Leader fails?                           | 🔄 A new Leader is elected automatically.                                                                                                                                                                                               |
| 2️⃣9️⃣ | What is Leader Election?                                    | 🗳️ Process of choosing a new Leader after failure.                                                                                                                                                                                     |
| 3️⃣0️⃣ | Does a single-node etcd need Leader Election?               | ❌ No                                                                                                                                                                                                                                    |
| 3️⃣1️⃣ | Minimum recommended etcd members for HA?                    | ✅ 3                                                                                                                                                                                                                                     |
| 3️⃣2️⃣ | Recommended odd number of members?                          | 3️⃣, 5️⃣, or 7️⃣                                                                                                                                                                                                                        |
| 3️⃣3️⃣ | Why use an odd number of members?                           | ⚖️ To achieve quorum efficiently.                                                                                                                                                                                                       |
| 3️⃣4️⃣ | What is Quorum?                                             | 📊 Majority of members required to make decisions.                                                                                                                                                                                      |
| 3️⃣5️⃣ | Quorum in a 3-node cluster?                                 | ✅ 2 members                                                                                                                                                                                                                             |
| 3️⃣6️⃣ | Quorum in a 5-node cluster?                                 | ✅ 3 members                                                                                                                                                                                                                             |
| 3️⃣7️⃣ | Can a cluster operate without quorum?                       | ❌ No                                                                                                                                                                                                                                    |
| 3️⃣8️⃣ | Is etcd strongly consistent?                                | ✅ Yes                                                                                                                                                                                                                                   |
| 3️⃣9️⃣ | Which protocol does etcd use for communication?             | 🌐 gRPC over HTTP/2                                                                                                                                                                                                                     |
| 4️⃣0️⃣ | Default client port?                                        | 🔌 2379                                                                                                                                                                                                                                 |
| 4️⃣1️⃣ | Default peer port?                                          | 🔌 2380                                                                                                                                                                                                                                 |
| 4️⃣2️⃣ | Which command lists etcd members?                           | `etcdctl member list`                                                                                                                                                                                                                   |
| 4️⃣3️⃣ | Which command checks etcd health?                           | `etcdctl endpoint health`                                                                                                                                                                                                               |
| 4️⃣4️⃣ | Which command checks endpoint status?                       | `etcdctl endpoint status`                                                                                                                                                                                                               |
| 4️⃣5️⃣ | Which tool is used to interact with etcd?                   | 🛠️ `etcdctl`                                                                                                                                                                                                                           |
| 4️⃣6️⃣ | Command to create an etcd snapshot?                         | `etcdctl snapshot save backup.db`                                                                                                                                                                                                       |
| 4️⃣7️⃣ | Command to restore a snapshot?                              | `etcdctl snapshot restore backup.db`                                                                                                                                                                                                    |
| 4️⃣8️⃣ | Why take etcd backups?                                      | 💾 Disaster recovery.                                                                                                                                                                                                                   |
| 4️⃣9️⃣ | Does Kubernetes automatically back up etcd?                 | ❌ Not by default.                                                                                                                                                                                                                       |
| 5️⃣0️⃣ | What happens if etcd is unavailable?                        | 🚨 Kubernetes cannot reliably read or write cluster state.                                                                                                                                                                              |
| 5️⃣1️⃣ | Does kube-apiserver work without etcd?                      | ❌ It cannot persist or retrieve cluster state if etcd is unavailable.                                                                                                                                                                   |
| 5️⃣2️⃣ | Where is etcd data stored on disk (kubeadm)?                | 📂 `/var/lib/etcd`                                                                                                                                                                                                                      |
| 5️⃣3️⃣ | Is etcd encrypted by default?                               | ⚠️ Communication is typically secured with TLS, but stored Secrets are **not encrypted at rest by default** unless encryption at rest is configured.                                                                                    |
| 5️⃣4️⃣ | Best practice for Secrets in etcd?                          | 🔒 Enable **Encryption at Rest** and RBAC.                                                                                                                                                                                              |
| 5️⃣5️⃣ | Can etcd be scaled horizontally?                            | ✅ Yes, by adding members (typically up to 5 or 7).                                                                                                                                                                                      |
| 5️⃣6️⃣ | What is the maximum recommended cluster size?               | 🎯 Usually **3 or 5** members; 7 is possible but less common due to coordination overhead.                                                                                                                                              |
| 5️⃣7️⃣ | Which Kubernetes installation manages etcd automatically?   | ☁️ Amazon EKS (managed control plane).                                                                                                                                                                                                  |
| 5️⃣8️⃣ | Which installation requires manual etcd backup?             | 🖥️ Self-managed clusters (e.g., kubeadm).                                                                                                                                                                                              |
| 5️⃣9️⃣ | Biggest advantage of etcd?                                  | 🚀 Reliable, distributed, strongly consistent storage for Kubernetes state.                                                                                                                                                             |
| 6️⃣0️⃣ | One-line interview answer?                                  | 💡 **etcd is Kubernetes' distributed key-value database that stores the entire cluster state. The `kube-apiserver` is the only core component that communicates directly with etcd, and `Raft ensures high availability and consistency`.** |
