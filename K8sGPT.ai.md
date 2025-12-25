### 🤔 Why use K8sGPT and what are the benefits?
K8sGPT exists for one simple reason:
```
🎯 Kubernetes tells you what failed — K8sGPT tells you why and how to fix it.
🎯 K8sGPT uses AI to analyze Kubernetes state and events, identify root causes, 
       and explain failures in plain English, reducing troubleshooting time and dependency on logs.
```
### 🔹 STEP 1: Install K8sGPT CLI
```
curl -s https://raw.githubusercontent.com/k8sgpt-ai/k8sgpt/main/install.sh | bash
```
Verify:
```
k8sgpt version
```
### 🔹 STEP 2: Configure AI backend (OpenAI example)
🤖 Configure AI backend in K8sGPT

K8sGPT needs one AI backend to turn raw Kubernetes errors into explanations.
Without a backend, it will still scan — but won’t explain.

Add backend:
```
k8sgpt auth add --backend openai --model gpt-4o
```
Check:
```
k8sgpt auth list
```
### 🔹 STEP 3: Run K8sGPT on EKS (basic scan)
```
k8sgpt analyze
```
```
This scans:
Pods
Deployments
Services
PVCs
Nodes
Events
```
### 🔹 STEP 4: Analyze a specific namespace (BEST PRACTICE)
```
k8sgpt analyze --namespace monitoring
```
With explanation:
```
k8sgpt analyze --namespace monitoring --explain
k8sgpt analyze --filters=PVC --explain
```

### (Optional) Run K8sGPT inside the cluster (Helm)
```
helm repo add k8sgpt https://charts.k8sgpt.ai
helm repo update
```
Install:
```
helm install k8sgpt k8sgpt/k8sgpt \
  --namespace k8sgpt \
  --create-namespace
```
🔐 Uses in-cluster RBAC → no kubeconfig needed on laptops.

### ✅ K8sGPT Commands
| Category              | Command                                                | What it does                  | When to use                |
| --------------------- | ------------------------------------------------------ | ----------------------------- | -------------------------- |
| 🔍 Verify install     | `k8sgpt version`                                       | Shows installed version       | First check after install  |
| 🔐 Backend check      | `k8sgpt auth list`                                     | Lists configured AI backends  | To confirm AI is set       |
| 🔐 Add AI backend     | `k8sgpt auth add --backend openai --model gpt-4o`      | Adds OpenAI backend           | Mandatory for explanations |
| 🔐 Remove backend     | `k8sgpt auth remove openai`                            | Removes backend config        | Key rotation / change      |
| 🔎 Basic scan         | `k8sgpt analyze`                                       | Scans cluster for issues      | Quick health check         |
| 🧠 AI explain         | `k8sgpt analyze --explain`                             | Explains errors using AI      | **Most used command**      |
| 🎯 Namespace scan     | `k8sgpt analyze -n monitoring --explain`               | Scans only one namespace      | Reduce noise               |
| 🧹 Filter resources   | `k8sgpt analyze --filters=Pod,PVC`                     | Scans only selected resources | Focused troubleshooting    |
| 🔎 Filter + namespace | `k8sgpt analyze -n monitoring --filters=PVC --explain` | PVC-only scan in namespace    | Storage issues             |
| 📊 Verbose output     | `k8sgpt analyze --explain --verbose`                   | Shows detailed reasoning      | Deep debugging             |
| 📦 JSON output        | `k8sgpt analyze --output json`                         | Machine-readable output       | CI/CD / automation         |
| 🧪 In-cluster logs    | `kubectl logs -n k8sgpt deploy/k8sgpt`                 | View K8sGPT pod logs          | Helm-based install         |
| ❓ Help               | `k8sgpt analyze --help`                                | Shows all options             | Learn flags                |











