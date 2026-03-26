## 🔐 Certificate Authority (CA)
👉 A CA (Certificate Authority) is a trusted entity that issues certificates.

Examples:
  - Public: Let’s Encrypt, DigiCert
  - Internal: HashiCorp Vault

#### ✔️ CA ensures: Server identity is valid & Clients can trust connection

## 🔐 Where TLS is Used in Kubernetes?
 ### 🧩 Core Components:
   - 🔗 API Server (HTTPS for kubectl)
   - 💾 etcd (encrypted storage communication)
   - 🖥️ kubelet ↔ API Server (mTLS)
   - ⚙️ Controller Manager & Scheduler
   - 🌐 Ingress (HTTPS for apps)
   - 🔄 Admission Webhooks

## 🔐 Self-Signed Certificate
### 📌 What is it?
  - Certificate signed by itself (not CA)
  - ✔️ Use Cases: Internal apps & Testing environments
  - ❌ Problems: Browsers don’t trust & No third-party validation

## ⏰ Certificate Expiry Monitoring
- Use cert-manager + Prometheus alerts & cert-manager metrics
- Set alerts before expiry

## ❌ What Happens if Certificate Expires?
- TLS connection fails 🚫
- Browser shows warning ⚠️
- Clients reject connection
- ✔️ Solution: Use cert-manager auto-renewal

---

### 🔥 Auto TLS with cert-manager
👉 Best practice (Production)

Steps:
  1. Install cert-manager
  2. Create ClusterIssuer
  3. Annotate Ingress
  ```
    cert-manager.io/cluster-issuer: letsencrypt-prod
  ```
   4. ✔️ Automatic certificate creation & renewal

## 🏢 Certificate Authority (CA)
A CA is a trusted entity that signs certificates.

### 🌍 Examples:
- Let's Encrypt
- DigiCert
- HashiCorp Vault

## 🤝 TLS Handshake
Process:
1️⃣ Exchange keys  
2️⃣ Verify certificates  
3️⃣ Establish secure connection  

---

## 🔄 Certificate Renewal / Rotation
🛠️ kubeadm:
```
kubeadm certs renew <component>
```
 - cert-manager: ✔️ Auto-renew before expiry (Auto-renewal enabled)
 - Manual: ✔️ Regenerate → Update secrets → Restart pods

---

## 🔐 Self-Signed vs CA-Signed
| Type | Trust Level |
|------|------------|
| Self-Signed | ❌ Not trusted |
| CA-Signed | ✅ Trusted |

## ☸️ Kubernetes Control Plane Security
### 🔑 Control Plane Communication :

👉 Central hub = API Server

- API Server is central hub  
- All components communicate via API Server securely
- API Server ↔ etcd → TLS encryption ( etcd only talks to API Server )
- Scheduler & Controller Manager → API Server  
- kubelet → API Server (mTLS authentication)  

### 📂 Certificates stored in:
```
/etc/kubernetes/pki/
```

## 🧠 Architecture Summary
- 🔑 API Server = central control point  
- 🔐 TLS secures all communication  
- 🗄️ etcd stores cluster state  
- 🤖 kubeadm auto-generates certs  

---

## ✨ Summary

TLS in Kubernetes:
- 🔐 Secures all components  
- 🔁 Supports mTLS (zero trust)  
- 🤖 Automatable with cert-manager  
- 🚀 Essential for production clusters  

Secure cluster = Reliable cluster 💙
