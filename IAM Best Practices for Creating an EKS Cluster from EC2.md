# 🚀 AWS IAM Best Practices for Creating an EKS Cluster from EC2

## 🔐 1. Use IAM Role Instead of Access Keys
 * Attach an IAM Role to the EC2 instance.
 * 🎯 Benefits:
   - ✅ No hardcoded credentials
   - 🔄 Automatic credential rotation
   - 🛡️ More secure

## 👤 2. Create an IAM Role for EC2
  * 🏷️ **Role Name:** -- EKS-Admin-Role
  * 🤝 **Trusted Entity:** -- AWS Service → EC2

## 📋 3. Attach Required Policies

```hcl
✅ AmazonEKSClusterPolicy
✅ AmazonEKSWorkerNodePolicy
✅ AmazonEKS_CNI_Policy
✅ AmazonEC2FullAccess
✅ AmazonVPCFullAccess
✅ IAMFullAccess
✅ CloudFormationFullAccess
✅ AmazonSSMManagedInstanceCore
```

## 🔗 5. Attach Role to EC2

```hcl
EC2
 └── Actions
      └── Security
           └── Modify IAM Role
```

 * ✅ Select: EKS-Admin-Role

## 🔍 6. Verify IAM Role

```bash
aws sts get-caller-identity
```

 * 🎯 Expected output:

```json
{
  "UserId": "AROAXXXXX",
  "Account": "123456789012",
  "Arn": "arn:aws:sts::123456789012:assumed-role/EKS-Admin-Role/i-12345"
}
```

## ☸️ 7. Create EKS Cluster Using eksctl

```bash
eksctl create cluster --name dev-cluster --region ap-south-1 --version 1.34 --nodegroup-name workers --node-type t3.medium --nodes 2
```

## 🔑 Enable IRSA

 * Use IAM Roles for Service Accounts (IRSA) instead of storing AWS credentials inside pods.

```hcl
Pod
 └── ServiceAccount
      └── IAM Role
```

 * 📦 Examples:
    - 🌐 ExternalDNS
    - ⚖️ AWS Load Balancer Controller
    - 📈 Cluster Autoscaler
    - 💾 EBS CSI Driver

---

# ✅ Interview Answer

 * 🎯 If I am creating EKS from an EC2 instance, an `IAM User is not required`.
 * 🔐 I attach an `IAM Role to the EC2 instance`, and AWS CLI automatically uses the role credentials.
 * 💡 IAM Users are typically used when creating EKS from a `local machine` with `access keys`.

###  When is IAM User Required❓

 * IAM User is needed only when:
    * 💻 Creating EKS from your `laptop/local machine`
    * 🔑 Using `AWS CLI` configured with `access keys`

 * 🧪 For labs, I use `AdministratorAccess`.
 * 🏭 In production, I follow `least-privilege` and grant only the permissions required for `EKS, EC2, IAM, VPC, and CloudFormation resources`.

🎉 **Best Practice:** IAM Role on EC2 + IRSA for Pods = 🔒 Secure & Production-Ready EKS Setup.
