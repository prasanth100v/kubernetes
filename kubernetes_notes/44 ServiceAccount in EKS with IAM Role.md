# 🔶 ServiceAccount in EKS with IAM Role (IRSA) 
## 🌟 What is ServiceAccount in EKS?

#### In Amazon EKS, pods don’t directly use AWS credentials:
👉 Instead, they use:

- 📦 ServiceAccounts → Used by Pods (Inside Kubernetes identity)
- 🔐 IAM Role (AWS permissions) → Used for AWS access (S3, DynamoDB, etc.)
- 🌉 OIDC (secure bridge between both)

👉 EKS uses **OIDC (OpenID Connect)** as a secure bridge between Kubernetes and AWS IAM.

---
#### 🤖 What is a ServiceAccount?
👉 A ServiceAccount in Kubernetes is:
- ✔️ An identity used by pods
- ✔️ Helps pods authenticate inside the cluster
- ✔️ Can be linked to AWS IAM for external access

## 🧠 Simple Understanding

- Pod → Uses ServiceAccount  
- ServiceAccount → Linked to IAM Role  
- IAM Role → Gives AWS permissions  

👉 No hardcoded credentials needed ✅

---

## 🔐 What is OIDC?
👉 OIDC = OpenID Connect : OIDC is a secure identity provider
 
🔍 Simple Meaning: A secure identity bridge between Kubernetes and AWS IAM
- ✔️ Verifies pod identity
- ✔️ Allows AWS to trust Kubernetesy

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
👉 Required once per cluster
```
eksctl utils associate-iam-oidc-provider --cluster <cluster-name> --approve
```
### Using AWS CLI:
Verify:
```
aws eks describe-cluster \
  --name <cluster-name> \
  --query "cluster.identity.oidc.issuer" \
  --output text
```
---

### If not enabled:
```
aws eks update-cluster-config --name <cluster-name> --resources-vpc-config oidc={enabled=true}
```
---

### Verify:
```
aws iam list-open-id-connect-providers | grep <CLUSTER_NAME>
```
---

## 🟢 Step 2: Create IAM Role (Trust Policy)

👉 This role:
- Trusts OIDC
- Grants AWS permissions (e.g., S3 access)

### 🧾 Trust Policy Example
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::<ACCOUNT_ID>:oidc-provider/oidc.eks.<REGION>.amazonaws.com/id/<OIDC_ID>"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "oidc.eks.<REGION>.amazonaws.com/id/<OIDC_ID>:sub": "system:serviceaccount:<NAMESPACE>:<SERVICE_ACCOUNT_NAME>"
        }
      }
    }
  ]
}
```
---
### 🔍 Explanation (Very Important)
| Field     | Meaning                                     |
| --------- | ------------------------------------------- |
| Federated | OIDC provider ARN                           |
| Action    | Allows pod to assume role                   |
| Condition | Restricts access to specific ServiceAccount |

👉 Only that Pod can use this IAM role

## 🔍 Replace Values

- ACCOUNT_ID → Your AWS Account ID  
- REGION → us-west-2 (example)  
- OIDC_ID → From cluster  
- NAMESPACE → default  
- SERVICE_ACCOUNT → s3-reader  

---

---

## 🟢 Step 3: Create IAM Role
```
aws iam create-role \
--role-name EKS-S3-ReadOnly-Role \
--assume-role-policy-document file://trust-policy.json
```
---

## 🟢 Step 4: Attach Permissions
```
aws iam attach-role-policy \
--role-name EKS-S3-ReadOnly-Role \
--policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
```
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
#### 👉 This annotation is the magic link between Kubernetes & AWS
---

## 🟢 Apply:
```
kubectl apply -f serviceaccount.yaml
```
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
```
kubectl exec -it s3-access-pod -- aws s3 ls
```
👉 If working → S3 access is successful ✅

---

## 🌍 Real-Time Use Case

👉 A Pod needs access to S3: ❌ Don’t store AWS credentials  ✅ Use IRSA  

### 🔐 Security Magic (Why IRSA is Powerful)

- ✔️ Secure
- ✔️ No hardcoded credentials
- ✔️ Uses temporary tokens
- ✔️ Fine-grained access control
- ✔️ Scoped to specific pods
- ✔️ Least privilege 
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
- ✔️ You need both together 

---

## 🔄 How Everything Works Together

- IAM Policy → AWS access (S3, DynamoDB)  
- OIDC → Identity bridge  
- ServiceAccount → Pod identity  
- RBAC → Kubernetes permissions

## 🔗 How They Work Together

- IAM → Allows pod to talk to AWS (S3, etc.)
- RBAC → Controls pod access inside Kubernetes

---

## 🧠 Summary (Simple)

1. Enable OIDC  → Trust bridge
2. Create IAM Role  → AWS permissions
3. Link ServiceAccount  → Link to IAM
4. Deploy Pod  → Uses ServiceAccount
5. Pod gets secure AWS access (no secrets)

---

## 🎯 Final Key Point

👉 No secrets stored  
👉 Uses temporary credentials  
👉 Fully secure access  

---
👉 “IRSA allows Kubernetes pods to securely access AWS services by linking ServiceAccounts to IAM roles using OIDC, eliminating the need for static credentials.”
