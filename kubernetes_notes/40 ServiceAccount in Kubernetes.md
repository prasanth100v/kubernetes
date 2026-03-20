# 🔐 ServiceAccount in Kubernetes (Easy + Complete Guide)

---

## 🌟 What is a ServiceAccount?

A **ServiceAccount** is an identity used by **Pods** to interact with the Kubernetes API server securely.

### 🧠 Simple Understanding
- 👤 Humans → use `kubectl`
- 📦 Pods → use **ServiceAccounts**

👉 Just like users need credentials, Pods also need identity and permissions.

👉 User accounts are for humans  
👉 ServiceAccounts are for Pods  

---

## 🎯 Purpose

- 🔹 Identity for Pods (not humans)
- 🔹 Namespace-scoped
- 🔹 Secure communication with Kubernetes API

---

## 🏠 Real-Life Analogy

- 👨 User → Employee ID card  
- 📦 Pod → ServiceAccount  
- 🏢 Kubernetes → Office  

👉 No ID → No access  
👉 With ID → Controlled access  

---

## 🛠 Default ServiceAccount

### 🔹 Default Behavior

If you create a Pod **without specifying a ServiceAccount**:

- Kubernetes automatically assigns the **default ServiceAccount**
- Every namespace has one default ServiceAccount
- Used automatically if none is specified

### 🔍 Check:
```bash
kubectl get serviceaccount
```

---

## 🧠 Key Concepts

Default:
Every namespace has a default ServiceAccount.

Token:
Automatically mounted inside Pod at:
/var/run/secrets/kubernetes.io/serviceaccount/

Used to authenticate API requests.

RBAC:
ServiceAccount gives identity only.❗
Permissions come from:
- 🧾 Role → what actions allowed
- 🔗 RoleBinding → who gets permission

---

## 🔐 Flow

1. Pod is created  
2. ServiceAccount is attached  
3. Token is injected into Pod  
4. Pod calls Kubernetes API  
5. RBAC checks permissions  
6. Access allowed or denied  

---

## 🚀 Why Use ServiceAccounts
- 🔐 Secure API access
- 🎯 Control permissions
- ⚙️ Required for automation tools

---

## 🌍 Real-Time Use Cases
- ⚙️ CI/CD → ArgoCD, Jenkins
- 📊 Monitoring → Prometheus
- 📜 Logging → Fluentd
- 🔑 Secrets → Vault, AWS IAM

---

## 🧾 Create ServiceAccount
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-sa              # 🏷️ Name of the ServiceAccount
  namespace: default       # 📦 Namespace (change if needed)
```
---

## 🔗 Use in Pod / Deployment
```
apiVersion: v1
kind: Pod
metadata:
  name: my-pod             # 🏷️ Pod name
spec:
  serviceAccountName: my-sa   # 🔗 Attach ServiceAccount to Pod
  containers:
    - name: my-container
      image: nginx           # 🌐 Sample container image
```
---

## 🔐 Give Permissions
### 🧾 Role (Defines what actions are allowed)
```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: read-pods
  namespace: default
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get","list"]
```
---

## 🔗 RoleBinding (Attach Role to ServiceAccount)
```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: bind-read-pods
  namespace: default
subjects:
- kind: ServiceAccount
  name: my-service-account
  namespace: default
roleRef:
  kind: Role
  name: read-pods
  apiGroup: rbac.authorization.k8s.io
```
---

## ❓ Interview Questions

#### What happens if you don’t specify a ServiceAccount?
👉 Default ServiceAccount is used.

#### How do you assign a ServiceAccount?
👉 Use serviceAccountName in Pod spec. (serviceAccountName: my-service-account) 

#### Can ServiceAccount access Kubernetes API?
👉 Yes, using the token.

#### What happens internally?
👉 Token is injected into Pod and used for authentication.

#### What is the purpose of RoleBinding?
👉 Defines what the Pod can do using that ServiceAccount.

---

## ⚖️ User vs ServiceAccount

User:
- Humans
- External
- kubeconfig authentication

ServiceAccount:
- Pods
- Internal
- Token authentication

---

## ☁️ AWS EKS (IRSA)

👉 To access AWS services:

- ❌ Don’t use IAM user credentials
- ✅ Use IRSA (IAM Role for ServiceAccount)

- ✔ Secure
- ✔ No secrets needed
---
### 🧾 Common Mistakes

- ❌ Giving admin access to default ServiceAccount
- ❌ Not using RBAC
- ❌ Hardcoding credentials in Pods

### 💡 Best Practices
- ✅ Create custom ServiceAccounts
- ✅ Use minimal permissions (least privilege)
- ✅ Avoid using default ServiceAccount
- ✅ Rotate tokens if needed

### 🧠 Quick Revision

- ServiceAccount = Pod identity
- Token = authentication
- RBAC = authorization
- RoleBinding = permission link

## 🧾 Summary

- Every namespace has a default ServiceAccount
- Pods use ServiceAccounts for identity
- Tokens are automatically mounted
- RBAC controls permissions
- If not specified, default is used

---

## 🎯 One-Line Answer
👉 ServiceAccount is an identity used by Pods to securely access the Kubernetes API with permissions controlled by RBAC.


# BASIC COMMANDS
```
# 📋 List ServiceAccounts
# kubectl get serviceaccounts

# ➕ Create ServiceAccount
# kubectl create serviceaccount my-sa

# 🔍 Describe ServiceAccount
# kubectl describe serviceaccount my-sa

# 🔑 View token inside Pod
# kubectl exec <pod> -- cat /var/run/secrets/kubernetes.io/serviceaccount/token

# 📂 Verify token mount
# kubectl exec <pod> -- ls /var/run/secrets/kubernetes.io/serviceaccount

# ❌ Delete ServiceAccount
# kubectl delete sa my-sa -n default
```
