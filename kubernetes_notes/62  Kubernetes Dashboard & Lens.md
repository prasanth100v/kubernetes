# 🌈 Kubernetes Dashboard & Lens 
## 📋 What is Kubernetes Dashboard?
 * **Kubernetes Dashboard** is a **web-based UI** to manage and visualize your cluster.
 * 👉 Helps with:
     - Monitoring resources 📊
     - Troubleshooting issues 🛠️
     - Managing workloads 🚀
  * 🎯 Kubernetes Dashboard = Web UI to manage Kubernetes clusters

### 🎉 What Can You Do?
   - 📦 View `Pods`, `Deployments`, `Services`
   - 📊 Monitor resource usage
   - 🔍 Debug issues
   - ⚙️ Deploy applications

### 🚀 Step 1: Deploy Dashboard
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```
 * Installs:
   - Dashboard UI  
   - Metrics Scraper  

### 📁 Step 2: Create Service Account
Create file: `eks-admin-service-account.yaml`

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: eks-admin
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: eks-admin-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: eks-admin
  namespace: kube-system
```
```bash
kubectl apply -f eks-admin-service-account.yaml
```

### 🔐 Step 3: Get Login Token
```bash
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep eks-admin | awk '{print $1}')
```
 * 👉 Copy token for login

### 🌐 Step 4: Access Dashboard
```bash
kubectl proxy
```
👉 Open browser:
```hcl
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```
👉 Login using token

### 🔓 External Access (Optional)
### 🌐 Expose via LoadBalancer
```bash
kubectl edit svc kubernetes-dashboard -n kubernetes-dashboard
```
Change:
```yaml
type: ClusterIP → LoadBalancer
```
### 🔍 Get External IP
```hcl
kubectl get svc -n kubernetes-dashboard
```

## 🔒 Security Note
 * 🚨 This setup gives cluster-admin access

#### ✅ Best Practice
  - Avoid cluster-admin in production ❌  
  - Use RBAC with limited access (permissions) ✅  

---

# 🚀 What is Lens?

 * Lens is a desktop-based Kubernetes GUI tool (like an `IDE` for Kubernetes).
 * 👉 Works on:
    - Windows
    - macOS
    - Linux

## 🎉 Why Lens is Powerful?
  - ✔ No need to install anything inside cluster
  - ✔ Works locally
  - ✔ Supports multiple clusters

### 🔧 Key Features
| 🧩 Feature           | 📖 Description           | 🧠 How It Helps                                   | 💡 Real-World Use Case      |
| -------------------- | ------------------------ | --------------------------------------------------- | --------------------------- |
| 🧭 **Navigator**     | View cluster resources   | 👉 Browse Pods, Nodes, Deployments, Services in UI | 🔍 Quick cluster exploration   |
| 📊 **Metrics**       | CPU & Memory usage       | 👉 Shows real-time resource consumption             | 🐛 Debug performance issues |
| 📜 **YAML Editor**   | Edit configurations      | 👉 Modify Kubernetes manifests directly in UI       | ⚡  Update deployments quickly |
| 🔍 **Logs**          | View container logs      | 👉 Stream logs from Pods                            | 🛠 Troubleshooting errors     |
| 🧩 **Extensions**    | Add plugins/tools        | 👉 Extend UI with custom tools                      | 🔧 Customize workflow       |
| 🌐 **Multi-cluster** | Manage multiple clusters | 👉 Connect and Switch between clusters (`Dev/Prod`) | ☁️ Centralized management   |

### 📷 What You Can Do with Lens
   - 👀 View all Pods
   - ✏️ Edit Deployment YAML
   - 📜 Check logs
   - 🖥️ Open terminal
   - 📈 Monitor resources
   - 🔄 Scale or restart apps

### 🛠️ When Should You Use Lens?
   - ✅ Visual debugging
   - ✅ YAML editing
   - ✅ Multi-cluster management
   - ✅ Faster troubleshooting

# ☁️ Add EKS Cluster in Lens
## Step 1: Install Lens
 * 👉 Download from: `https://k8slens.dev/`

## Step 2: Update kubeconfig
```bash
aws eks update-kubeconfig --name my-eks-cluster --region us-west-2 --alias my-eks-lens
```
## Step 3: Open Lens
  * Launch app

## Step 4: Add Cluster
  - Click `+ Add Cluster`
  - Auto-detects from `~/.kube/config`
  - Select your cluster

## 🚀 Step 5: Access Dashboard
  * Click Add → Open
  * 🎉 Done!, Full visual control of cluster

### 🔄 Lens Workflow
```yaml
Lens Desktop 💻 → kubeconfig 🔑 → Kubernetes Cluster 🚀
```

## ⚖️ Kubernetes Dashboard vs Lens
| 🧩 Feature           | 📋 **K8s Dashboard**                                            | 💻 **Lens**                                                                                                   |
| -------------------- | ---------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| 🖥 **Type**          | 🌐 Web UI (runs inside cluster)<br>Dashboard is browser-based    | 💻 Desktop Application<br>Lens runs locally                                                                   |
| ⚙️ **Setup**         | 📦 Install inside cluster                                       | 📥 Install locally                                                                                             |
| 🔐 **Security**      | 🛡 Requires RBAC setup<br>Dashboard requires manual auth setup   | 🔑 Uses kubeconfig<br>Lens integrates with existing configs                                                   |
| 🌐 **Multi-cluster** | ⚠️ Limited                                                      | ✅ Excellent support<br>Lens easily switches clusters                                                                |
| 🛠 **Debugging**     | 🙂 Basic (logs, events)                                          | 🚀 Advanced tools (metrics, logs, exec, terminal)<br>Lens provides deeper observability<br>Lens for troubleshooting  |

---

### 🔐 Best Practices
 * ✅ For Dashboard
    - Use RBAC
    - Avoid public exposure
    - Use HTTPS
 * ✅ For Lens
    - Secure kubeconfig
    - Use least privilege
    - Avoid sharing credentials
     
## 🎯 Final Summary

  - ✔ Dashboard 📋 → Simple UI inside cluster
  - ✔ Lens 💻      → Powerful IDE outside cluster 
  - ✔ Both simplify management  
  - ✔ Use Lens for better UX 🚀  

🎉 Happy Learning Kubernetes!

