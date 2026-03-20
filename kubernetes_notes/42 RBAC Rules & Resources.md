# 🔐 Kubernetes RBAC Rules & Resources (Easy + Complete Guide)

RBAC rules define access using apiGroups, resources, and verbs. These rules are attached to roles and assigned via bindings to control access securely in Kubernetes.
---

## 🌟 Structure of a Rule

Each RBAC rule defines **what access is allowed**.

### 🧠 A Rule Contains:

- 🔹 apiGroups → API group of the resource  
- 🔹 resources → What resources (pods, deployments, etc.)  
- 🔹 verbs → What actions are allowed  

---

## ⚙️ Common Verbs (Actions)

- get → Read single resource  
- list → List resources  
- watch → Watch changes  
- create → Create resource  
- update → Update resource  
- patch → Partial update  
- delete → Delete resource  
- deletecollection → Delete multiple resources  

---

## 📦 Example Rule

rules:
- apiGroups: [""]  
  resources: ["pods","services"]  
  verbs: ["get","list"]  

- apiGroups: ["apps"]  
  resources: ["deployments"]  
  verbs: ["create","update","delete"]  

---

## 🌍 Common Resources by API Group

### 1️⃣ Core API Group (apiGroups: [""])
- pods  
- services  
- configmaps  
- secrets  
- namespaces  
- persistentvolumes  
- persistentvolumeclaims  
- endpoints  

---

### 2️⃣ apps (apiGroups: ["apps"])
- deployments  
- replicasets  
- statefulsets  
- daemonsets  

---

### 3️⃣ batch (apiGroups: ["batch"])
- jobs  
- cronjobs  

---

### 4️⃣ rbac.authorization.k8s.io
- roles  
- rolebindings  
- clusterroles  
- clusterrolebindings  

---

### 5️⃣ networking.k8s.io
- ingresses  
- networkpolicies  
- ingressclasses  

---

### 6️⃣ autoscaling
- horizontalpodautoscalers  

---

### 7️⃣ policy
- poddisruptionbudgets  
- podsecuritypolicies (deprecated)  

---

### 8️⃣ storage.k8s.io
- storageclasses  
- csidrivers  
- csinodes  
- volumeattachments  

---

### 9️⃣ apiextensions.k8s.io
- customresourcedefinitions (CRDs)  

---

## 🔍 Useful Command

To list all resources in your cluster:

kubectl api-resources

---

## 🔐 Real-Time Use Cases

### ✅ 1. Read-only access to pods in dev namespace
- Create Role  
- Create RoleBinding  

---

### ✅ 2. Monitoring app access (Prometheus)
- Use ClusterRole  
- Use ClusterRoleBinding  

👉 Example:
Allow Prometheus to list pods across all namespaces:
- Create ClusterRole with list permission  
- Bind using ClusterRoleBinding  

---

### ✅ 3. Jenkins Deployment Access

Steps:
1. Create ServiceAccount for Jenkins  
2. Create Role (pods + deployments permissions)  
3. Create RoleBinding  

---

### ✅ 4. AWS IAM + Kubernetes (IRSA)

Use IRSA + RBAC together:

- IAM → AWS resource access  
- RBAC → Kubernetes access  

---

## ☁️ EKS Use Cases

### 🔹 Allow IAM role to list pods in namespace

Steps:
1. Map IAM role in aws-auth  
2. Create Role  
3. Create RoleBinding  

---

### 🔹 IAM Role → ServiceAccount Mapping

👉 Use IRSA (IAM Roles for Service Accounts)

⚠️ Important:
RBAC must ALSO allow access to Kubernetes resources.

---

### 🔹 Pod access to S3 (Secure way)

Steps:
1. Enable OIDC provider  
2. Create IAM policy (S3 access)  
3. Create IAM role with trust policy  
4. Annotate ServiceAccount with IAM role  
5. Use ServiceAccount in Pod  

---

## ❓ Common Questions

### ❓ Can multiple Pods use the same ServiceAccount?
👉 Yes.

✔ Multiple Pods can share one ServiceAccount  
✔ Useful when same permissions are needed  

---

## ⚠️ Important Notes

- RBAC = Authorization system  
- ServiceAccount = Identity  
- IAM = Cloud permissions  

👉 All work together for secure access  

---

## 🧠 Quick Revision

- apiGroups → Where resource belongs  
- resources → What you access  
- verbs → What you can do  
- Role → Namespace access  
- ClusterRole → Cluster access  

---

## 🎯 One-Line Answer

RBAC rules define which actions (verbs) are allowed on which resources within specific API groups.
