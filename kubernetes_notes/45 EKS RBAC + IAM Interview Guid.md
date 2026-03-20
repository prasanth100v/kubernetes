# 🔐 EKS RBAC + IAM (Important Notes & Interview Guide)

---

## ❓ What happens if a user has NO RoleBinding or ClusterRoleBinding?

👉 They **cannot perform any actions**

❌ Kubernetes will return:
**Forbidden Error**

💡 Reason:
- Authentication may succeed (IAM)
- But Authorization (RBAC) fails
- ❌ No permissions = No access

---

## 🔥 system:masters Group (Very Important)

- Built-in Kubernetes group  
- Has **cluster-admin permissions**  

### ⚠️ What it means:
- Full access to cluster  
- Bypasses ALL RBAC restrictions  

👉 Equivalent to **root/admin access**

---

## 🧪 kubectl auth can-i Command

### 🎯 Purpose:
Check if a user/service account has permission

### 📌 Example:
```
kubectl auth can-i create pods --as=dev-user -n dev
```
👉 Output:
- yes ✅ → allowed  
- no ❌ → denied  

---

## ✅ 1. How RBAC Works in EKS?

EKS uses:

- ☁️ IAM → Authentication  
- 🔐 RBAC → Authorization  

### 🔄 Flow:
1. IAM user/role logs in  
2. aws-auth maps to Kubernetes user/group  
3. RBAC decides permissions  

---

## 🧾 2. What is aws-auth ConfigMap?

- Located in: kube-system namespace  
- Used to map IAM → Kubernetes  

---

### 📌 Example:
```
mapUsers:
- userarn: arn:aws:iam::111122223333:user/dev-user  
  username: dev-user  
  groups:
    - dev-group  
```
---

## ⚠️ Important Limitation

### ❌ IAM Groups are NOT supported

👉 aws-auth supports ONLY:
- IAM Users  
- IAM Roles  

### ✅ Solution:
Attach same IAM Role to multiple users

---

## 🧪 Testing Access in EKS

### 📌 Command:
```
kubectl auth can-i get pods --as=dev-user -n dev
```
👉 Checks:
- If IAM mapping + RBAC both allow access  

---

## ❌ What if IAM user is NOT mapped in aws-auth?

👉 User CANNOT access cluster  

Even if:
- RBAC permissions exist ❗

💡 Reason:
- Authentication fails  

---

## ⚖️ Important Concept

👉 BOTH are required:

- IAM → Who are you  
- RBAC → What can you do  

❌ Missing any → Access denied  

---

## 🔗 ServiceAccount + IAM (IRSA)

### ❓ Can they be connected?

👉 YES ✅ using IRSA (IAM Roles for Service Accounts)

---

### 🎯 What it does:

- Links Kubernetes ServiceAccount → IAM Role  
- Allows Pods to access AWS services  

---

### 💡 Example Use:

- Pod → Access S3  
- Pod → Access DynamoDB  

✔ No credentials stored  
✔ Secure access  

---

## 🧠 Quick Revision

- No RoleBinding → ❌ No access  
- system:masters → 🔥 Full admin  
- aws-auth → IAM mapping  
- kubectl auth can-i → Permission check  
- IAM + RBAC → Both required  
- IRSA → Pod to AWS access


# 🔑 Kubernetes RBAC Key Concepts 
## 🌟 RBAC = Who Can Do What

RBAC controls:

- 👤 Who → User / Group / ServiceAccount  
- ⚙️ What action → get, list, create, delete  
- 📦 On what resource → pods, secrets, deployments  

👉 RBAC = Who + Action + Resource

---

## 🎯 Role vs ClusterRole

### 🔹 Role
- Namespace-level access  
- Use when restricting access within ONE namespace  

---

### 🔹 ClusterRole
- Cluster-wide access  
- Use for:
  - All namespaces  
  - Non-namespaced resources (nodes, PVs)  

---

## 🔐 Important Concept

### RBAC ONLY controls Kubernetes API access

❌ It does NOT control:
- Container-level permissions  
- Network traffic  
- Pod-to-Pod communication  

---

## 🔑 Authentication vs Authorization

- 🔐 Authentication → Who you are  
  (IAM, Certificates, Tokens)

- 🔒 Authorization (RBAC) → What you can do  

👉 Both are required  

---

## 🔐 Key RBAC Rules

### 🧾 1. Roles = What can be done

Define:

- Verbs:
  - get, list, watch → Read  
  - create, update, patch, delete → Write  
  - * → All actions  

- Resources:
  - pods  
  - services  
  - deployments  
  - secrets  

---

### 🔗 2. Bindings = Who can do it

Subjects can be:

- 👤 User  
- 👥 Group  
- 📦 ServiceAccount  

---

## ⚠️ Important Warning

❌ Avoid using:
system:masters  

👉 Because:
- Gives FULL admin access  
- Bypasses all RBAC rules  

---

## 🔐 Security Best Practices

- ✅ Follow least privilege principle  
- ✅ Give only required permissions  
- ✅ Avoid "*" (wildcards) in resources  
- ✅ Be very strict with secrets access  
- ✅ Use groups instead of individual users  

---

## 📂 Git Best Practice

Store all RBAC YAML files in Git:

- Role  
- RoleBinding  
- ClusterRole  
- ClusterRoleBinding  

👉 Benefits:
- Version control  
- Easy auditing  
- Change tracking  

---

## 🧠 Quick Revision

- RBAC = Who + What + Resource  
- Role → Namespace  
- ClusterRole → Cluster  
- Binding → Assign permission  
- Avoid system:masters  
- Avoid "*" in roles  

---

## 🎯 One-Line Answer

RBAC controls who can perform what actions on which Kubernetes resources using roles and bindings.
