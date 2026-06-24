# 🔶 ServiceAccount in EKS with IAM Role (IRSA) 
## 🌟 What is ServiceAccount in EKS?
 * 📦 IRSA (IAM Roles for Service Accounts) is an EKS feature that allows Kubernetes Pods to securely access AWS services using `IAM Roles` attached to `ServiceAccounts`.
 * 🔐 It uses the `EKS OIDC provider` and AWS STS (`Security Token Service`) to provide temporary credentials, enabling Least AWS permissions at the `Pod level` instead of the `worker-node` level.
 * 👉 EKS uses **OIDC (OpenID Connect)** as a secure bridge between `Kubernetes` and `AWS IAM`.
 
### 🤖 What is a ServiceAccount?
 * 👉 A ServiceAccount (SA) is an `identity` used by Pods to communicate with the `Kubernetes API Server`.
 * 👉 Pod uses a ServiceAccount token for authentication.
   * Simple Understanding :
       * Pod → Uses `ServiceAccount`
       * ServiceAccount → Linked to `IAM Role`
       * IAM Role → Gives `AWS permissions  `
       * 👉 No hardcoded credentials needed ✅

---

## 🔐 What is OIDC?
 * 👉 OIDC = OpenID Connect : OIDC is a `secure identity provider`
 * 🔍 Simple Meaning:
     * A secure identity `bridge` between `Kubernetes` and `AWS IAM`
     * Verifies `pod identity`
     * Allows AWS to `trust` Kubernetes

## ☸️ EKS IRSA (IAM Roles for Service Accounts) — Secure AWS Access from Pods
| 🔢 **Step** | 📖 **What You Do**                           | 🧠 **How It Works**                                       | 💡 **Real-World Insight**         |
| ----------- | -------------------------------------------- | --------------------------------------------------------- | --------------------------------- |
| 1️⃣         | 🔐 Enable OIDC Provider                      | Connects EKS cluster identity with AWS IAM                | Required for IRSA                 |
| 2️⃣         | 🤖 Create IAM Role (`Trust Policy`)          | Allows a Kubernetes ServiceAccount to assume the IAM role | Defines AWS permissions           |
| 3️⃣         | 👤 Create ServiceAccount & Annotate IAM Role | Link IAM Role using `eks.amazonaws.com/role-arn` annotation | Bridge between Kubernetes and AWS |
| 4️⃣         | 📦 Deploy Pod with ServiceAccount            | Pod uses the annotated ServiceAccount                     | Pod gets IAM identity               |
| 5️⃣         | ☁️ Access AWS Services Securely              | Pod obtains temporary STS credentials automatically       | No hardcoded access keys 🔐       |

---

## 🟢 Step 1: Enable OIDC Provider
### Using eksctl:
👉 Required once per cluster
```yaml
eksctl utils associate-iam-oidc-provider --cluster <cluster-name> --approve
```
### Using AWS CLI:
Verify:
```yaml
aws eks describe-cluster \
  --name <cluster-name> \
  --query "cluster.identity.oidc.issuer" \
  --output text
```

### If not enabled:
```yaml
aws eks update-cluster-config --name <cluster-name> --resources-vpc-config oidc={enabled=true}
```

### Verify:
```yaml
aws iam list-open-id-connect-providers | grep <CLUSTER_NAME>
```

## 🟢 Step 2: Create IAM Role (Trust Policy)

 * 👉 This role:
   - Trusts OIDC
   - Grants AWS permissions (e.g., S3 access)

### 🧾 Trust Policy Example
```yaml
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

### 🔍 Explanation (Very Important)
| 🧩 Field         | 💡 Meaning                                                        |
| ---------------- | ------------------------------------------------------------------ |
| 🔗 **Federated** | 🌐 OIDC provider ARN (`EKS identity provider`)                     |
| ⚙️ **Action**    | 🔑 Allows Pod to assume IAM role (`sts:AssumeRoleWithWebIdentity`) |
| 🎯 **Condition** | 🔒 Restricts access to specific ServiceAccount                     |

👉 Only that Pod can use this IAM role

## 🔍 Replace Values
  - ACCOUNT_ID → Your `AWS Account ID  `
  - REGION → us-west-2 (example)  
  - OIDC_ID → From cluster  
  - NAMESPACE → default  
  - SERVICE_ACCOUNT → `s3-reader ` 

## 🟢 Step 3: Create IAM Role
```yaml
aws iam create-role \
--role-name EKS-S3-ReadOnly-Role \
--assume-role-policy-document file://trust-policy.json
```

## 🟢 Step 4: Attach Permissions
```yaml
aws iam attach-role-policy \
--role-name EKS-S3-ReadOnly-Role \
--policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
```

## ✅ What Happens Now?
  - Only Pods using this ServiceAccount can use the role  
  - Pods get temporary credentials  
  - No secrets stored  

## 🟢 Step 5: Create ServiceAccount (Link IAM Role)
```yaml
apiVersion: v1  
kind: ServiceAccount  
metadata:  
  name: s3-access-sa  
  namespace: default  
  annotations:  
    eks.amazonaws.com/role-arn: arn:aws:iam::1234567890:role/S3ReaderRole  
```
 👉 This annotation is the magic link between `Kubernetes & AWS`

## 🟢 Apply:
```yaml
kubectl apply -f serviceaccount.yaml
```

## 🟢 Step 6: Deploy Pod with ServiceAccount
```yaml
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

## 🔐 Token Location in Pod
```yaml
/var/run/secrets/eks.amazonaws.com/serviceaccount/token
```

## 🟢 Step 7: Verify Access
```yaml
kubectl exec -it s3-access-pod -- aws s3 ls
```
  👉 If working → S3 access is successful ✅

---

## 🌍 Real-Time Use Case

 * 👉 A Pod needs access to S3: ❌ Don’t store AWS credentials  ✅ Use `IRSA `
 * 🔐 Security Magic (Why IRSA is Powerful) :
     * ✔️ Secure
     * ✔️ No hardcoded credentials
     * ✔️ Uses temporary tokens
     * ✔️ Fine-grained access control
     * ✔️ Scoped to specific pods
     * ✔️ Least privilege 

## ⚖️ EKS Access Layers
| 🧩 Layer                    | 🎯 Purpose                             | 📖 What It Controls                                                    | ⚙️ Managed By | 💡 Real-World Example                             |
| ---------------------------- | --------------------------------------- | ------------------------------------------------------------------------- | ------------- | ------------------------------------------------- |
| 🔹 **IAM (Authentication)** | 🔑 Identify **who** can access EKS     | 👉 Verifies identity (user/role) before allowing access                   | ☁️ AWS        | 👤 IAM user trying to access cluster via `kubectl` |
| 🔹 **RBAC (Authorization)** | 🎯 Define **what actions** are allowed | 👉 Controls permissions (`get`, `list`, `create`, `delete`) inside cluster | ⚙️ Kubernetes | 🔍 User can view Pods but cannot delete        |

## ⚠️ Important Notes
  * ❌ IAM alone is NOT enough
  * ❌ RBAC alone is NOT enough
  * ✅ Both are required together

---

## 🔄 How Everything Works Together
| 🧩 Component      | 💡 Meaning                                      |
| ----------------- | ------------------------------------------------ |
| ☁️ IAM Policy     | 🔑 AWS access (`S3`, `DynamoDB`, etc.)          |
| 🔗 OIDC           | 🌐 Identity bridge between `EKS` & `AWS IAM`    |
| 👤 ServiceAccount | 🆔 Pod identity inside Kubernetes               |
| 🛡️ RBAC          | 🎯 Kubernetes permissions (`what Pod/user can do`) |

### 🔗 How They Work Together
   - IAM → Allows `pod to talk to AWS` (S3, etc.)
   - RBAC → Controls `pod access` inside Kubernetes


## 🧠 Summary 
1. Enable OIDC  → Trust bridge
2. Create IAM Role  → AWS permissions
3. Link ServiceAccount  → Link to IAM
4. Deploy Pod  → Add ServiceAccount name in Deployment yaml
5. Pod gets secure AWS access (`no secrets`)

## 🎯 Final Key Point
   * 👉 No secrets stored
   * 👉 Uses temporary credentials
   * 👉 Fully secure access
   * 👉 IRSA allows Kubernetes pods to securely access AWS services by linking `ServiceAccounts` to `IAM roles using OIDC`, eliminating the need for `static credentials`.
