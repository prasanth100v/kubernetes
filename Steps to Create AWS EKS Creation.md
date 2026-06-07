# 🚀 Steps to Create AWS EKS

### 🔐 Step 1: Create IAM Role for EC2
 * 👤 Create an IAM Role and attach: `AdministratorAccess`
     * 🏭 For production, use `least-privilege policies`, but for learning/labs `AdministratorAccess` is easiest.
 * 🖥️ Launch EC2 Instance
 * 🔗 Attach IAM Role to EC2
     * ⚙️ EC2 → Actions → Security → Modify IAM Role
     * ✅ IAM User is `not required` If I am creating the EKS cluster from an EC2 instance. I attached an IAM Role to the EC2 instance, and AWS CLI automatically uses the role credentials.
     * 💡 IAM Users are typically used when creating `EKS from a local machine` with `access keys`.

### 🛠️ Step 2: Install AWS CLI, kubectl & eksctl
  * ⚙️ chmod +x eks_tools_setup.sh
  * ▶️ ./eks_tools_setup.sh

📄 eks_tools_setup.sh
```hcl
#!/bin/bash

echo "==== Installing unzip package ===="
sudo yum install -y unzip 2>/dev/null || sudo apt-get install -y unzip

echo "==== Installing AWS CLI v2 ===="
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version

echo "==== Installing kubectl ===="
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.26.4/2023-05-11/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/kubectl
kubectl version --client

echo "==== Installing eksctl ===="
curl --silent --location \
"https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" \
| tar xz -C /tmp

sudo mv /tmp/eksctl /usr/local/bin/eksctl
eksctl version

echo "==== Installation Completed Successfully! ===="
```
### 📝 Step 3: Create Cluster YAML
📄 vi ekscluster.yml
```yaml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: eks-cluster
  region: ap-south-1
  version: "1.35"

managedNodeGroups:
  - name: node-group-1
    instanceType: t3.medium
    desiredCapacity: 2
    minSize: 2
    maxSize: 3
```

### ☸️ Step 4: Create EKS Cluster
```hcl
eksctl create cluster -f ekscluster.yml
```
 * ⏳ This takes: 15-25 minutes

### 🔍 Step 9: Verify Cluster
📋 List clusters:
```hcl
aws eks list-clusters
```
🔄 Update kubeconfig:
```hcl
aws eks update-kubeconfig \
--region ap-south-1 \
--name eks-cluster
```
### ✅ Step 5: Verify Everything
```hcl
kubectl get nodes

kubectl get pods -A

kubectl get svc

kubectl get deployments
```
### 🗑️ ✅ Delete EKS Cluster using eksctl (Most Common)
```
eksctl delete cluster --name eks-cluster --region ap-south-1
```

### 🎤 Interview Answer (2 Minutes)
 * 🚀 First, I launch an Amazon `Linux EC2 instance` and attach an `IAM role with EKS permissions`. Then I install `AWS CLI`, `kubectl`, and `eksctl`.
 * 📄 I create an` EKS cluster configuration YAML `file specifying the `cluster name`,` Kubernetes version`, `region`, and `managed node group details`.
 * ⚙️ Using `eksctl create cluster -f ekscluster.yml`, AWS provisions the control plane, VPC, networking, and worker nodes.
 * ✅ After creation, I update kubeconfig using `aws eks update-kubeconfig`, verify the cluster with `kubectl get nodes`, and deploy applications using `Kubernetes manifests`.

### 🔑 EKS Accesss
  * 🎯 EKS has two levels of access.
  * 🔐 `IAM permissions` allow access to `AWS resources`, while `Kubernetes RBAC controls` access `inside the cluster`.
  * ⚠️ Even if a user has AdministratorAccess in AWS, they cannot view Kubernetes resources unless they are added to the EKS Access Entries or mapped through `aws-auth`.

---

 * 💡 AWS CLI command lists all EC2 instance types that AWS currently marks as `Free Tier eligible` in your account/region.
 * 🖥️ Select `instance type` and update instance in `ekscluster.yaml` managedNodeGroups
```hcl
aws ec2 describe-instance-types \
  --filters Name=free-tier-eligible,Values=true \
  --query "InstanceTypes[*].InstanceType"
```
 * 📋 Typical output:
```hcl
    "c7i-flex.large",
    "t4g.small",
    "t3.micro",
    "t4g.micro",
    "t3.small",
    "m7i-flex.large"
```
### ⚠️ Important for EKS
 * 🚨 Not all "free-tier-eligible" instances are suitable for EKS:

| Instance Type  | Architecture | EKS Worker Node |
| -------------- | ------------ | --------------- |
| t3.micro       | x86          | ❌ Too small     |
| t3.small       | x86          | ❌ Too small     |
| t4g.micro      | ARM          | ❌ Too small     |
| t4g.small      | ARM          | ❌ Too small     |
| c7i-flex.large | x86          | ✅ Good          |
| m7i-flex.large | x86          | ✅ Good          |
