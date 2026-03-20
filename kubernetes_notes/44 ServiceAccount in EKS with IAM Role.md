# 🔶 ServiceAccount in EKS with IAM Role (IRSA) – Easy + Complete Guide

---

## 🌟 What is ServiceAccount in EKS?

In Amazon EKS:

- 📦 ServiceAccounts → Used by Pods (inside Kubernetes)
- ☁️ IAM Roles → Used for AWS access (S3, DynamoDB, etc.)

👉 EKS uses **OIDC (OpenID Connect)** as a secure bridge between Kubernetes and AWS IAM.

---

## 🧠 Simple Understanding

- Pod → Uses ServiceAccount  
- ServiceAccount → Linked to IAM Role  
- IAM Role → Gives AWS permissions  

👉 No hardcoded credentials needed ✅

---

## 🔐 What is OIDC?

OIDC is a secure identity provider that allows:

👉 Kubernetes Pods → to assume AWS IAM roles securely

---

## 🎯 Overall Flow

1. Enable OIDC  
2. Create IAM Role (with trust policy)  
3. Create ServiceAccount (link IAM role)  
4. Deploy Pod using ServiceAccount  
5. Pod accesses AWS securely  

---

## 🟢 Step 1: Enable OIDC Provider

### Using eksctl:
eksctl utils associate-iam-oidc-provider --cluster <cluster-name> --approve

---

### Using AWS CLI:
aws eks describe-cluster --name <cluster-name> --query "cluster.identity.oidc.issuer" --output text

---

### If not enabled:
aws eks update-cluster-config --name <cluster-name> --resources-vpc-config oidc={enabled=true}

---

### Verify:
aws iam list-open-id-connect-providers | grep <CLUSTER_NAME>

---

## 🟢 Step 2: Create IAM Role (Trust Policy)

### 🧾 Trust Policy Example
```
{
"Version":"2012-10-17",
"Statement": [
{
"Effect":"Allow",
"Principal": {
"Federated":"arn:aws:iam::1234567890:oidc-provider/oidc.eks.us-west-2.amazonaws.com/id/EXAMPLEC0FFEE123"
},
"Action":"sts:AssumeRoleWithWebIdentity",
"Condition": {
"StringEquals": {
"oidc.eks.us-west-2.amazonaws.com/id/EXAMPLEC0FFEE123:sub":"system:serviceaccount:default:s3-reader"
}
}
}
]
}
```
---

## 🔍 Replace Values

- ACCOUNT_ID → Your AWS Account ID  
- REGION → us-west-2 (example)  
- OIDC_ID → From cluster  
- NAMESPACE → default  
- SERVICE_ACCOUNT → s3-reader  

---

## 🧠 Explanation

- Federated → OIDC provider of EKS  
- Action → Allows web identity login  
- Condition → Restricts access to specific ServiceAccount  

👉 Only that Pod can use this IAM role

---

## 🟢 Step 3: Create IAM Role

aws iam create-role \
--role-name EKS-S3-ReadOnly-Role \
--assume-role-policy-document file://trust-policy.json

---

## 🟢 Step 4: Attach Permissions

aws iam attach-role-policy \
--role-name EKS-S3-ReadOnly-Role \
--policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess

---

## ✅ What Happens Now?

- Only Pods using this ServiceAccount can use the role  
- Pods get temporary credentials  
- No secrets stored  

---

## 🟢 Step 5: Create ServiceAccount (Link IAM Role)
```
apiVersion: v1  
kind: ServiceAccount  
metadata:  
  name: s3-access-sa  
  namespace: default  
  annotations:  
    eks.amazonaws.com/role-arn: arn:aws:iam::1234567890:role/S3ReaderRole  
```
---

## 🟢 Apply:

kubectl apply -f serviceaccount.yaml

---

## 🟢 Step 6: Deploy Pod with ServiceAccount
```
apiVersion: apps/v1  
kind: Deployment  
metadata:  
  name: my-app  
spec:  
  replicas: 1  
  selector:  
    matchLabels:  
      app: my-app  
  template:  
    metadata:  
      labels:  
        app: my-app  
    spec:  
      serviceAccountName: my-app-sa  
      containers:  
      - name: app  
        image: myimage  
```
---

## 🔐 Token Location in Pod
```
/var/run/secrets/eks.amazonaws.com/serviceaccount/token
```
---

## 🟢 Step 7: Verify Access

kubectl exec -it s3-access-pod -- aws s3 ls

👉 If working → S3 access is successful ✅

---

## 🌍 Real-Time Use Case

👉 A Pod needs access to S3:

❌ Don’t store AWS credentials  
✅ Use IRSA  

✔ Secure  
✔ Temporary credentials  
✔ Least privilege  

---

## ⚖️ EKS Access Layers

### 🔹 1. IAM (Authentication)
- Who can access EKS  
- Managed by AWS  

---

### 🔹 2. RBAC (Authorization)
- What they can do in cluster  
- Managed by Kubernetes  

---

## ⚠️ Important Notes

- IAM alone is NOT enough  
- RBAC is also required  
- Always use least privilege  

---

## 🔄 How Everything Works Together

- IAM Policy → AWS access (S3, DynamoDB)  
- OIDC → Identity bridge  
- ServiceAccount → Pod identity  
- RBAC → Kubernetes permissions  

---

## 🧠 Summary (Simple)

1. Enable OIDC  
2. Create IAM Role  
3. Link ServiceAccount  
4. Deploy Pod  
5. Pod accesses AWS securely  

---

## 🎯 Final Key Point

👉 No secrets stored  
👉 Uses temporary credentials  
👉 Fully secure access  

---
