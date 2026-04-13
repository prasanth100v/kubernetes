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
Cluster Autoscaler uses IAM Roles for Service Accounts (IRSA), which needs OIDC.

``` bash
eksctl utils associate-iam-oidc-provider   --region <your-region>   --cluster <your-cluster-name>   --approve
```
✅ This enables your cluster to authenticate IAM roles through service accounts.

## 🔸 Step 2: Create IAM Policy for Cluster Autoscaler

Download the policy JSON:
``` bash
curl -O https://raw.githubusercontent.com/kubernetes/autoscaler/master/clusterautoscaler/cloudprovider/aws/examples/cluster-autoscaler-policy.json
```

Create the IAM policy:
``` bash
aws iam create-policy   --policy-name AmazonEKSClusterAutoscalerPolicy   --policy-document file://cluster-autoscaler-policy.json
```
✅ This policy gives permissions to interact with EC2 Auto Scaling Groups.


## 🔹 Step 3: Create IAM Role and Kubernetes Service Account (IRSA)

Replace `<account-id>` and `<your-cluster-name>`.

``` bash
eksctl create iamserviceaccount   --name cluster-autoscaler   --namespace kube-system   --cluster my-eks-cluster   --attach-policy-arn arn:aws:iam::<account-id>:policy/AmazonEKSClusterAutoscalerPolicy   --approve   --override-existing-serviceaccounts
```
✅ This connects the IAM role with the Kubernetes service account (IRSA).

## 🔹 Step 4: Tag Your Auto Scaling Group (Node Group)
Go to **AWS Console → EC2 → Auto Scaling Groups → your node group → Add Tags**

| Key                                      | Value |
|------------------------------------------|-------|
| k8s.io/cluster-autoscaler/enabled        | true  |
| k8s.io/cluster-autoscaler/my-eks-cluster | owned |

✅ These tags allow Cluster Autoscaler to discover and scale your node group.


## 🔹 Step 5: Deploy Cluster Autoscaler to EKS

Download autoscaler YAML:
``` bash
kubectl apply -f https://github.com/kubernetes/autoscaler/releases/latest/download/cluster-autoscaler-autodiscover.yaml
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
 - 🧩 Set the correct image version (e.g., `v1.28.x`)
 - ⚖️ Add `--balance-similar-node-groups`
 - 📦 Add `--skip-nodes-with-local-storage=false`

## 🔁 Optional Pod Annotation

``` yaml
annotations:
  cluster-autoscaler.kubernetes.io/safe-to-evict: "false"
```

### ✅ Why we use this annotation?

 * This tells the Cluster Autoscaler: ❌ **Do NOT evict this pod** when trying to scale down a node.
 * 📌 Use Case Example :
   * If you have:
      * 📊 DaemonSet pod like log collector (`Fluentd`)
      * 🗄️ Database pod
      * ⚠️ Critical stateful workload
      * Use this annotation to prevent disruption.

## 🔹 Step 6: Verify Setup

Check autoscaler pod:
``` bash
kubectl get pods -n kube-system | grep cluster-autoscaler
```

View logs:
``` bash
kubectl logs -f deployment/cluster-autoscaler -n kube-system
```

---

# 📊 Summary Table

1️⃣ Enable OIDC  
2️⃣ Create IAM Policy  
3️⃣ Create IAM Service Account using IRSA  
4️⃣ Tag your Node Group ASG  
5️⃣ Deploy and configure Cluster Autoscaler  
6️⃣ Watch logs and test scaling  
