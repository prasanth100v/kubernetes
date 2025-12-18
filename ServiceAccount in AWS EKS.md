1️⃣ ServiceAccount → exists ONLY in Kubernetes (EKS), NOT in AWS IAM.
```
kubectl get sa -n kube-system
```
🔹 This ServiceAccount:

Exists only inside the EKS cluster
Is stored in etcd (Kubernetes)
❌ Will NOT appear in AWS IAM
❌ Cannot be seen in AWS Console → IAM → Users/Roles

🧠 Common confusion (very important)

❌ Wrong thinking  -->  “ServiceAccount is created in IAM”

### ✅ Correct thinking
“ServiceAccount exists in Kubernetes, IAM Role exists in AWS, and IRSA securely links them using OIDC.”

### Why ServiceAccount is important in AWS EKS
A ServiceAccount (SA):
ServiceAccounts provide authentication + authorization for pods.
Is a namespaced Kubernetes object
Represents the identity of a pod
Controls what a pod is allowed to do

In EKS, ServiceAccounts are used with IRSA
👉 IAM Roles for Service Accounts

This allows:

Pods to securely access AWS services
No AWS keys inside containers
Least-privilege access

3️⃣ Without ServiceAccount (❌ Old / Not Recommended)

Earlier approach:

IAM permissions attached to worker node role
Every pod on that node got same permissions
❌ Security risk

4️⃣ With ServiceAccount + IRSA (✅ Recommended)
Each app gets its own IAM role via ServiceAccount.

Example:

EBS CSI Driver
Needs permission to create EBS volumes
Uses ebs-csi-controller-sa
That ServiceAccount is linked to an IAM role
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: ebs-csi-controller-sa
  namespace: kube-system
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::<ACCOUNT_ID>:role/EBS-CSIDriver-Role
```
Now:

Only EBS CSI pods get EBS permissions
Other pods ❌ cannot create volumes

7️⃣ How to see which pods use a ServiceAccount
```
kubectl get pod <pod-name> -n kube-system -o yaml | grep serviceAccountName
```

In EKS, a pod assumes an IAM role using its ServiceAccount via OIDC. Kubernetes issues a short-lived token, AWS Security Token Service (AWS STS) validates it, 
and the pod receives temporary credentials without storing any secrets.

