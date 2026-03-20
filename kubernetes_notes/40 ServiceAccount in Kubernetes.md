# 🔐 ServiceAccount in Kubernetes (Easy + Complete Guide)

## 🌟 What is a ServiceAccount?
A ServiceAccount in Kubernetes is an identity used by Pods to interact with the Kubernetes API server securely.

Humans use kubectl to access the cluster.
Pods use ServiceAccounts to access the cluster.

User accounts are for humans.
ServiceAccounts are for Pods.

---

## 🎯 Purpose
- Identity for Pods (not humans)
- Namespace scoped
- Secure API communication

---

## 🏠 Real-Life Analogy
User → Employee ID  
Pod → ServiceAccount  
Kubernetes → Office  

Without ID → No access  
With ID → Controlled access  

---

## 🛠 Default ServiceAccount

If you do NOT specify a ServiceAccount in a Pod:
- Kubernetes automatically assigns the default ServiceAccount
- Every namespace has one default ServiceAccount

Check:
kubectl get serviceaccount

---

## 🧠 Key Concepts

Default:
Every namespace has a default ServiceAccount.

Token:
Automatically mounted inside Pod at:
/var/run/secrets/kubernetes.io/serviceaccount/

Used to authenticate API requests.

RBAC:
ServiceAccount gives identity only.
Permissions come from:
- Role
- RoleBinding

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
- Secure API access
- Control permissions
- Required for automation tools

---

## 🌍 Real-Time Use Cases
- CI/CD → ArgoCD, Jenkins
- Monitoring → Prometheus
- Logging → Fluentd
- Secrets → Vault, AWS IAM

---

## 🧾 Create ServiceAccount

apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-service-account
  namespace: default

---

## 🔗 Use in Pod

spec:
  serviceAccountName: my-service-account
  containers:
  - name: my-container
    image: nginx

---

## 🔐 Role

apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: read-pods
  namespace: default
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get","list"]

---

## 🔗 RoleBinding

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

---

## ❓ Interview Questions

What happens if you don’t specify a ServiceAccount?
Default ServiceAccount is used.

How do you assign a ServiceAccount?
Use serviceAccountName in Pod spec.

What happens internally?
Token is injected into Pod and used for authentication.

What is RoleBinding?
It defines what the Pod can do.

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

To access AWS services:
Do NOT use IAM user credentials.
Use IAM Role for ServiceAccount (IRSA).

---

## 🧾 Summary

- Every namespace has a default ServiceAccount
- Pods use ServiceAccounts for identity
- Tokens are automatically mounted
- RBAC controls permissions
- If not specified, default is used

---

## 🎯 One-Line Answer

ServiceAccount is an identity used by Pods to securely access the Kubernetes API with permissions controlled by RBAC.
