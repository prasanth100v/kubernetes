## 🏭 Production-Grade Kubernetes Cluster on AWS

To run Kubernetes reliably in **production on AWS**, the following core services and components are typically required:

---

### ☸️ Kubernetes Control Plane
- **Amazon EKS** (recommended) or **self-managed Kubernetes**
- **EKS with Fargate** (optional)  
  → Serverless Kubernetes (no EC2 node management)

---

### 🖥️ Compute
- **Amazon EC2** – Worker nodes for Kubernetes
- **EC2 Auto Scaling Groups** – Automatically scale worker nodes based on demand

---

### 🔐 Security & Identity
- **AWS IAM** – Authentication and authorization
- **IAM Roles for Service Accounts (IRSA)** – Fine-grained pod-level permissions
- **AWS KMS** – Encryption for secrets and data
- **AWS Secrets Manager** – Secure storage for application secrets

---

### 🌐 Networking
- **Amazon VPC** – Isolated networking for the cluster
- **Security Groups & NACLs** – Network security
- **ALB / NLB** – Load balancing for Kubernetes Services and Ingress

---

### 📦 Container Registry
- **Amazon ECR** – Store and manage Docker container images

---

### 🔑 TLS & Certificates
- **AWS Certificate Manager (ACM)** – TLS certificates for Ingress (HTTPS)

---

### 🗄️ Storage & Databases
- **Amazon RDS / Amazon Aurora** – Managed databases for stateful workloads
- **Amazon S3** – Object storage for:
  - Logs
  - Backups
  - Helm charts
  - Artifacts

---

### 📊 Logging & Monitoring
- **Amazon CloudWatch** – Logs, metrics, and alarms for Kubernetes clusters
- (Optional) **Prometheus + Grafana** – Advanced monitoring & visualization

---

### 🔄 Auto Scaling
- **Horizontal Pod Autoscaler (HPA)** – Scale pods automatically
- **Cluster Autoscaler** – Scale worker nodes automatically
- **EC2 Auto Scaling** – Infrastructure-level scaling

---

### 🧩 Optional Production Add-Ons
- **Helm** – Kubernetes package management
- **Ingress Controller (AWS Load Balancer Controller)**
- **Service Mesh (Istio / App Mesh)** – Traffic management & observability
- **Backup Tools (Velero)** – Disaster recovery

---

## ✅ Summary

A production-grade Kubernetes cluster on AWS typically includes:

- EKS for control plane
- EC2 or Fargate for compute
- IAM, KMS, and Secrets Manager for security
- ALB/NLB for traffic management
- RDS/S3 for data storage
- CloudWatch for observability
- Auto Scaling at both pod and node levels

This setup ensures **high availability, scalability, security, and fault tolerance** for real-world workloads.

---

# AWS EKS (Elastic Kubernetes Service) Setup Guide

## Step 1: Install Required Tools
Install the following tools on your local machine:

- AWS CLI
- kubectl (Kubernetes CLI)
- eksctl (EKS setup tool)
  
### eks_tools_setup.sh  
```yaml
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
##✅ scripts-for-eks (🚀 How to Use)
```yaml
vi eks tools setup.sh
chmod +x eks tools setup.sh
./eks tools setup.sh
```

## Configure AWS CLI
```yaml
aws configure
```
Provide:
- Access Key
- Secret Key
- Region (example: us-west-2)
- Output: json

## Create EKS Cluster yaml
#### ekscluster.yml
```yaml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig
metadata:
  name: eks-cluster
  region: ap-south-1
  version: "1.29"
managedNodeGroups:
  - name: node-group-1
    instanceType: t3.medium
    desiredCapacity: 2
    minSize: 2
    maxSize: 3
```
### ✅ Create EKS Cluster using eksctl command
```yaml
eksctl create cluster -f ekscluster.yml
```
### Verify EKS Cluster
```yaml
kubectl get nodes
```
If nodes show Ready, your AWS EKS cluster is successfully created.

