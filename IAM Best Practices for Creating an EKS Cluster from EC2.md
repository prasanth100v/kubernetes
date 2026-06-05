# AWS IAM Best Practices for Creating an EKS Cluster from EC2
## 1. Use IAM Role Instead of Access Keys
 * Attach an IAM Role to the EC2 instance.
 * Benefits:
   - No hardcoded credentials
   - Automatic credential rotation
   - More secure

## 2. Create an IAM Role for EC2
  * **Role Name:** -- EKS-Admin-Role
  * **Trusted Entity:** -- AWS Service → EC2

## 3. Attach Required Policies
```hcl
- AmazonEKSClusterPolicy
- AmazonEKSWorkerNodePolicy
- AmazonEKS_CNI_Policy
- AmazonEC2FullAccess
- AmazonVPCFullAccess
- IAMFullAccess
- CloudFormationFullAccess
- AmazonSSMManagedInstanceCore
```

## 5. Attach Role to EC2
```hcl
EC2
 └── Actions
      └── Security
           └── Modify IAM Role
```
 * Select: EKS-Admin-Role

## 6. Verify IAM Role
```hcl
aws sts get-caller-identity
```
 * Expected output:
```json
{
  "UserId": "AROAXXXXX",
  "Account": "123456789012",
  "Arn": "arn:aws:sts::123456789012:assumed-role/EKS-Admin-Role/i-12345"
}
```

## 7. Create EKS Cluster Using eksctl

```bash
eksctl create cluster --name dev-cluster --region ap-south-1 --version 1.34 --nodegroup-name workers --node-type t3.medium --nodes 2
```

## Enable IRSA
 * Use IAM Roles for Service Accounts (IRSA) instead of storing AWS credentials inside pods.
```hcl
Pod
 └── ServiceAccount
      └── IAM Role
```
 * Examples:
    - ExternalDNS
    - AWS Load Balancer Controller
    - Cluster Autoscaler
    - EBS CSI Driver

---

## 10. How do you securely create an EKS cluster from an EC2 instance?**
 * I launch an EC2 administration server and attach an IAM Role with the required EKS, EC2, IAM PassRole, and CloudFormation permissions.
 * I use that role with eksctl, Terraform, or AWS CLI to create the EKS cluster. I avoid AWS access keys on the server and follow the principle of least privilege.
 * For workloads running inside the cluster, I use `IAM Roles for Service Accounts (IRSA)` to provide secure AWS access.

