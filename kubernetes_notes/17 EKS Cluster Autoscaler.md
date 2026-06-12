# 🧱 3. Cluster Autoscaler

## 📌 What it does
 * Cluster Autoscaler automatically adds or removes nodes in the cluster based on:
     * ➕ Pods that can't be scheduled → **adds nodes**
     * ➖ Nodes that are underutilized → **removes nodes**

## 🌐 Requirements
  - ☁️ Works with cloud providers (`AWS`, `GCP`, `Azure`, etc.)
  - 🔗 Integrated with node groups (e.g., `EKS Managed Node Group` or `ASG` AWS Auto Scaling Groups)

## 🌲 What is EKS Cluster Autoscaler?

  * EKS Cluster Autoscaler automatically `adds` or `removes` EC2 worker nodes in our EKS node group when pods can't be scheduled due to `insufficient resources`.
  * 👉 It works at the **infrastructure (node) level**, not the pod level.

## ✅ Real-Time Use Case
   - 🚀 Your microservice gets a traffic spike. HPA increases pod replicas, but there is no space on current nodes.
   - ⚙️ Cluster Autoscaler `launches` new EC2 nodes to fit the new pods.
   - 🌙 Traffic is low at night, and your app only needs 2 nodes instead of 6.
   - 💰 Cluster Autoscaler deletes unused nodes (if pods can run elsewhere), saving cost.

---

# 🔹 Step-by-Step Setup of Cluster Autoscaler in EKS
## 🧰 Prerequisites

 - ✅ EKS cluster already created
 - 🖥️ At least one managed node group (Amazon EC2)
 - 🛠️ kubectl, eksctl, and AWS CLI configured
 - 🔐 IAM OIDC provider is enabled for the cluster

## 🔸 Step 1: Enable OIDC on the EKS Cluster
  * Cluster Autoscaler uses IAM Roles for Service Accounts (IRSA), which needs OIDC.
``` hcl
eksctl utils associate-iam-oidc-provider   --region <your-region>   --cluster <your-cluster-name>   --approve

--- OR---

eksctl utils associate-iam-oidc-provider \
  --region ap-south-1 \
  --cluster eks-cluster2 \
  --approve
```

 * Verify OIDC issuer
```hcl
aws eks describe-cluster \
  --name eks-cluster2 \
  --region ap-south-1 \
  --query "cluster.identity.oidc.issuer" \
  --output text
```

* Verify IAM OIDC Provider
```hcl
aws iam list-open-id-connect-providers
```

### Why do we associate an IAM OIDC provider with an EKS cluster?
  * 🛠️ The IAM OIDC provider enables IAM Roles for Service Accounts (`IRSA`).
  * 🔐 IRSA allows Kubernetes Pods to `securely assume IAM roles` and access AWS services `without storing AWS access` keys inside containers.
  * ✅ Now that OIDC is enabled, you can create IAM Roles for Service Accounts (IRSA) and install:
       * AWS Load Balancer Controller
       * Amazon EBS CSI Driver
       * Cluster Autoscaler
       * Pods to access Amazon S3

## 🔸 Step 2: Create IAM Policy for Cluster Autoscaler
 * Create file:
```hcl
vim cluster-autoscaler-policy.json
```
```JSON
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "autoscaling:DescribeAutoScalingGroups",
        "autoscaling:DescribeAutoScalingInstances",
        "autoscaling:DescribeLaunchConfigurations",
        "autoscaling:DescribeScalingActivities",
        "autoscaling:DescribeTags",
        "autoscaling:SetDesiredCapacity",
        "autoscaling:TerminateInstanceInAutoScalingGroup",
        "ec2:DescribeLaunchTemplateVersions"
      ],
      "Resource": "*",
      "Effect": "Allow"
    }
  ]
}
```

Create the IAM policy:
``` hcl
aws iam create-policy \
--policy-name AmazonEKSClusterAutoscalerPolicy \
--policy-document file://cluster-autoscaler-policy.json
```
 * ✅ This policy gives permissions to interact with EC2 Auto Scaling Groups.


## 🔹 Step 3: Create IAM Role and Kubernetes Service Account (IRSA)
  * Replace `<account-id>` and `<your-cluster-name>`.
  * Replace Account ID:
    ```hcl
    aws sts get-caller-identity
    ```

``` hcl
eksctl create iamserviceaccount \
  --cluster=eks-cluster2 \
  --namespace=kube-system \
  --name=cluster-autoscaler \
  --attach-policy-arn=arn:aws:iam::953146141152:policy/AmazonEKSClusterAutoscalerPolicy \
  --override-existing-serviceaccounts \
  --approve \
  --region ap-south-1
```
 * ✅ This connects the IAM role with the Kubernetes service account (`IRSA`).
 * Verify Service Account
   ```hcl
   kubectl get sa cluster-autoscaler -n kube-system
   kubectl describe sa cluster-autoscaler -n kube-system
   ```

  ### Find Auto Scaling Group Name
 ```hcl
aws autoscaling describe-auto-scaling-groups \
--region ap-south-1 \
--query "AutoScalingGroups[*].AutoScalingGroupName"

aws autoscaling describe-auto-scaling-groups \
--region ap-south-1
 ```
  
## 🔹 Step 4: Tag Your Auto Scaling Group (Node Group)
 * Add ASG NAME : `eks-workers-6ecf503a-13a1-a129-5b35-1a8116c8bfc7`
```hcl
aws autoscaling create-or-update-tags --tags \
ResourceId=<ASG-NAME>,ResourceType=auto-scaling-group,Key=k8s.io/cluster-autoscaler/enabled,Value=true,PropagateAtLaunch=true \
ResourceId=<ASG-NAME>,ResourceType=auto-scaling-group,Key=k8s.io/cluster-autoscaler/eks-cluster2,Value=owned,PropagateAtLaunch=true
```
| Key                                      | Value |
|------------------------------------------|-------|
| k8s.io/cluster-autoscaler/enabled        | true  |
| k8s.io/cluster-autoscaler/my-eks-cluster | owned |
 * ✅ These tags allow Cluster Autoscaler to discover and scale your node group.

### Increase ASG Max Size
```hcl
aws autoscaling update-auto-scaling-group \
  --auto-scaling-group-name eks-workers-6ecf503a-13a1-a129-5b35-1a8116c8bfc7 \
  --min-size 2 \
  --max-size 5 \
  --desired-capacity 2 \
  --region ap-south-1
```
 * Verify:
```hcl
aws autoscaling describe-auto-scaling-groups \
  --auto-scaling-group-name eks-workers-6ecf503a-13a1-a129-5b35-1a8116c8bfc7 \
  --region ap-south-1 \
  --query 'AutoScalingGroups[0].[MinSize,MaxSize,DesiredCapacity]'
```

## 🔹 Step 5: Deploy Cluster Autoscaler to EKS

Download autoscaler YAML:
``` bash
curl -O https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml
```

Edit deployment:
``` bash
kubectl edit deployment cluster-autoscaler -n kube-system
```

### 🔁 Update args section
``` yaml
containers:
- name: cluster-autoscaler
  image: k8s.gcr.io/autoscaling/cluster-autoscaler:v1.28.0
  command:
    - ./cluster-autoscaler
    - --v=4
    - --stderrthreshold=info
    - --cloud-provider=aws
    - --skip-nodes-with-local-storage=false
    - --expander=least-waste
    - --balance-similar-node-groups
    - --nodes=1:5:<your-ASG-name>
    - --cluster-name=my-eks-cluster
  env:
    - name: AWS_REGION
      value: us-west-2
```

### 🔁 Recommended Updates

 - 🔧 Update cluster name under `--cluster-name=<your-cluster-name>`
 - 🧩 Set the correct image version (e.g., `image: registry.k8s.io/autoscaling/cluster-autoscaler:v1.33.0`)
 - serviceAccountName: `cluster-autoscaler`
 - 🔁 Optional Pod Annotation
``` yaml
annotations:
  cluster-autoscaler.kubernetes.io/safe-to-evict: "false"
```
 * ✅ Why we use this annotation?
      * This tells the Cluster Autoscaler : ❌ **Do NOT evict this pod** when trying to scale down a node.
 * Apply Manifest
   ```hcl
   kubectl apply -f cluster-autoscaler-autodiscover.yaml
   ```

## 🔹 Step 6: Verify Setup

``` hcl          
kubectl get deployment cluster-autoscaler -n kube-system
kubectl get pods -n kube-system | grep cluster-autoscaler          # Check autoscaler pod
kubectl describe deployment cluster-autoscaler -n kube-system
kubectl logs deployment/cluster-autoscaler -n kube-system             # View logs
```

## Step 7: Test Autoscaling
 * Create workload:
 *  `vi cpu-demo.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cpu-demo
spec:
  replicas: 20
  selector:
    matchLabels:
      app: cpu-demo
  template:
    metadata:
      labels:
        app: cpu-demo
    spec:
      containers:
      - name: nginx
        image: nginx
        resources:
          requests:
            cpu: 500m
            memory: 512Mi
```

Apply:
```
kubectl apply -f cpu-demo.yaml
```

### Step 8: Watch Scaling

Pending Pods:
```hcl
kubectl get pods -w
```
Nodes:
```hcl
kubectl get nodes -w
```
Autoscaler Logs:
```hcl
kubectl logs -f deployment/cluster-autoscaler -n kube-system
```

---

## 📊 Summary Table

1️⃣ Enable OIDC  
2️⃣ Create IAM Policy  
3️⃣ Create IAM Service Account using IRSA  
4️⃣ Tag your Node Group ASG  
5️⃣ Deploy and configure Cluster Autoscaler  
6️⃣ Watch logs and test scaling  
