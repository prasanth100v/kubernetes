# 🔐 EKS RBAC + IAM (Important Notes & Interview Guide)

---

## ❓ What happens if a user has NO RoleBinding or ClusterRoleBinding?

👉 They **cannot perform any actions**

❌ Kubernetes will return:
**Forbidden Error**

💡 Reason:
- Authentication may succeed (IAM)
- But Authorization (RBAC) fails

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
kubectl auth can-i create pods --as=dev-user -n dev

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

mapUsers:
- userarn: arn:aws:iam::111122223333:user/dev-user  
  username: dev-user  
  groups:
    - dev-group  

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
kubectl auth can-i get pods --as=dev-user -n dev

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
