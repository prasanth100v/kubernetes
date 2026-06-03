# 🚀 K8sGPT (Kubernetes GPT)
| ❓ **Question**                                         | ✅ **Answer**                                                                                                                                                                                     |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 🔹 **1. What is K8sGPT?**                              | 🤖 K8sGPT is an AI-powered Kubernetes diagnostic tool that scans Kubernetes clusters, identifies issues, analyzes errors, and provides human-readable explanations with remediation suggestions. |
| 🔹 **2. Why was K8sGPT created?**                      | 🛠️ Kubernetes troubleshooting can be complex. K8sGPT simplifies debugging by converting technical Kubernetes errors into understandable explanations and recommended fixes.                     |
| 🔹 **3. Is K8sGPT an official Kubernetes project?**    | 🌐 No. K8sGPT is an open-source project developed by the Kubernetes community, not by Kubernetes maintainers directly.                                                                           |
| 🔹 **4. What problems does K8sGPT solve?**             | 🚨 It helps identify misconfigurations, failed deployments, pod crashes, service issues, ingress errors, storage problems, and security-related findings.                                        |
| 🔹 **5. How does K8sGPT work?**                        | ⚙️ It connects to a Kubernetes cluster, scans resources, collects events and errors, analyzes them, and optionally uses AI models to explain root causes and solutions.                          |
| 🔹 **6. Does K8sGPT replace kubectl?**                 | 🔧 No. K8sGPT complements kubectl by interpreting cluster issues rather than replacing cluster management commands.                                                                              |
| 🔹 **7. What AI models can K8sGPT use?**               | 🧠 OpenAI, Azure OpenAI, Ollama, Amazon Bedrock, Google Gemini, and other supported AI providers.                                                                                                |
| 🔹 **8. Is AI mandatory in K8sGPT?**                   | ❌ No. K8sGPT can detect issues without AI. AI is used only for generating explanations and recommendations.                                                                                      |
| 🔹 **9. How does K8sGPT identify issues?**             | 🔍 It analyzes Kubernetes API resources, events, pod statuses, deployments, services, PVCs, ingress resources, and node conditions.                                                              |
| 🔹 **10. What is the main command used for analysis?** | 🚀 `k8sgpt analyze`                                                                                                                                                                              |
---

## 🔹 STEP 1: Install K8sGPT CLI
```hcl
curl -s https://raw.githubusercontent.com/k8sgpt-ai/k8sgpt/main/install.sh | bash
```
Verify:
```
k8sgpt version
```

## 🔹 STEP 2: Configure AI backend (OpenAI example)
 * 🤖 Configure AI backend in K8sGPT
     * K8sGPT needs one AI backend to turn raw Kubernetes errors into explanations.
     * Without a backend, it will still scan — but won’t explain.

Add backend:
```hcl
k8sgpt auth add --backend openai --model gpt-4o
```
Check:
```hcl
k8sgpt auth list
```

## 🔹 STEP 3: Run K8sGPT on EKS (basic scan)
```hcl
k8sgpt analyze
```
 * This scans:
    * Pods
    * Deployments
    * Services
    * PVCs
    * Nodes and Events

## 🔹 STEP 4: Analyze a specific namespace (BEST PRACTICE)
```hcl
k8sgpt analyze --namespace monitoring
```
* With explanation:
```hcl
k8sgpt analyze --namespace monitoring --explain
k8sgpt analyze --filters=PVC --explain
```

## (Optional) Run K8sGPT inside the cluster (Helm)
```hcl
helm repo add k8sgpt https://charts.k8sgpt.ai
helm repo update
```
Install:
```hcl
helm install k8sgpt k8sgpt/k8sgpt \
  --namespace k8sgpt \
  --create-namespace
```
🔐 Uses in-cluster RBAC → no kubeconfig needed on laptops.

## CLI Installation (Optional)
### Using wget
```hcl
wget https://github.com/k8sgpt-ai/k8sgpt/releases/latest/download/k8sgpt_Linux_x86_64.tar.gz
tar -xzf k8sgpt_Linux_x86_64.tar.gz
sudo mv k8sgpt /usr/local/bin/
```

---

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
