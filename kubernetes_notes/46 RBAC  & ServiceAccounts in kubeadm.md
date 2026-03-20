# 🔐 RBAC Users & ServiceAccounts in kubeadm Cluster (Easy + Complete Guide)

---

## 👤 Creating Users for RBAC in kubeadm Cluster

In a kubeadm cluster, Kubernetes does NOT create users directly.

👉 You manually create:
- Private key  
- Certificate  
- kubeconfig context  

---

## 🟢 Step 1: Generate Private Key

openssl genrsa -out johndoe.key 2048

---

## 🟢 Step 2: Create CSR (Certificate Signing Request)

openssl req -new -key johndoe.key -out johndoe.csr -subj "/CN=johndoe/O=developers"

### 🧠 Important:
- CN (Common Name) → Username (johndoe)  
- O (Organization) → Group (developers)  

👉 Group is used later in RBAC

---

## 🟢 Step 3: Sign CSR with Kubernetes CA

sudo openssl x509 -req -in johndoe.csr \
-CA /etc/kubernetes/pki/ca.crt \
-CAkey /etc/kubernetes/pki/ca.key \
-CAcreateserial -out johndoe.crt -days 365

---

## 🟢 Step 4: Create Kubectl Context

kubectl config set-credentials johndoe \
--client-certificate=johndoe.crt \
--client-key=johndoe.key

kubectl config set-context johndoe-context \
--cluster=kubernetes \
--user=johndoe

---

## 🔐 RBAC Configuration

### 🧾 Create ClusterRole (Cluster-wide permissions)

apiVersion: rbac.authorization.k8s.io/v1  
kind: ClusterRole  
metadata:  
  name: viewer  
rules:  
- apiGroups: [""]  
  resources: ["pods","services","deployments"]  
  verbs: ["get","list","watch"]  

---

### 🔗 RoleBinding (Bind Role to User)

apiVersion: rbac.authorization.k8s.io/v1  
kind: RoleBinding  
metadata:  
  name: developer-binding  
  namespace: development  
subjects:  
- kind: User  
  name: johndoe  
  apiGroup: rbac.authorization.k8s.io  
roleRef:  
  kind: Role  
  name: developer-role  
  apiGroup: rbac.authorization.k8s.io  

---

## 🧪 Step 5: Verify Access

### Switch Context:
kubectl config use-context johndoe-context

---

### Test:
kubectl get pods -n development  
kubectl get nodes  

👉 Expected:
- pods → ✅ allowed  
- nodes → ❌ denied (if not permitted)  

---

# 🤖 Creating ServiceAccount for RBAC

ServiceAccounts are used by **Pods inside cluster** to talk to Kubernetes API.

---

## 🟢 Step 1: Create Namespace

kubectl create namespace dev

---

## 🟢 Step 2: Create ServiceAccount

apiVersion: v1  
kind: ServiceAccount  
metadata:  
  name: dev-app-sa  
  namespace: dev  

---

## 🟢 Step 3: Attach Permissions

👉 Create:
- Role  
- RoleBinding  

(define rules as per requirement)

---

## 🔐 How ServiceAccount Works

- Token is automatically mounted inside Pod  
- Used for authentication with API server  

👉 Token path:
/var/run/secrets/kubernetes.io/serviceaccount/

---

## 👥 Groups in Kubernetes

- Kubernetes does NOT manage groups directly  
- Groups come from:
  - OIDC  
  - IAM  
  - External providers  

👉 You can still:
- Define RBAC rules for a group  
- As long as user belongs to that group  

---

## ☁️ Important EKS Concept

### ❓ Can we use RBAC alone without aws-auth in EKS?

❌ No  

👉 You need BOTH:

- IAM (aws-auth) → Authentication  
- RBAC → Authorization  

---

## ⚖️ Key Differences

| Feature            | 👤 User (kubeadm) | 📦 ServiceAccount |
|------------------|------------------|------------------|
| Used by          | Humans           | Pods             |
| Auth method      | Certificate      | Token            |
| Created by       | Manual           | Kubernetes       |
| Scope            | External         | Internal         |

---

## 🧠 Quick Revision

- Users → Created using certificates  
- CN → Username  
- O → Group  
- RBAC → Controls permissions  
- ServiceAccount → Pod identity  
- Token → Auto-mounted  

---

## 🎯 One-Line Answer

In kubeadm, users are created using certificates and assigned RBAC roles, while ServiceAccounts are used by Pods with tokens for API authentication.
