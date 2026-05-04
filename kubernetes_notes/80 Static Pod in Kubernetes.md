# Static Pod in Kubernetes
 
 * A Static Pod in Kubernetes is a pod that is directly managed by the `kubelet` on a node — not by the Kubernetes API server or controllers like Deployments.
 * the kubelet automatically creates and manages it.
    * 👉 No `kubectl apply` needed
    * ❌ No scheduler involved
    * ❌ Runs only on that specific node (each node needs its `own manifest file`)
    * ❌ No ReplicaSet support
    * Used for `critical system` workloads
    * 👉 if kubelet restarts, Static Pods are `recreated` automatically from manifest files

 ### 🔹 Example Static Pod YAML
 ```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-static
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx
 ```

 ## 🔹 How Static Pods Work
   * You place a Pod YAML in a directory (e.g., 📌 Save this file in : `/etc/kubernetes/manifests/nginx-static.yaml`)
     * kubelet watches that directory
     * kubelet automatically creates the pod
     * If the `pod crashes → kubelet restarts it`
     * If you `delete the file` → pod is `deleted`
  
## Key Characteristics
| 🧩 Feature            | 💡 Static Pod                  |
| --------------------- | ------------------------------ |
| ⚙️ **Managed by**     | 🤖 kubelet                     |
| 📄 **Created via**    | 🖥 Local file (not API server) |
| 🎯 **Scheduler**      | ❌ Not used                     |
| 📍 **Runs on**        | 🧱 Specific node only          |
| 🔁 **Auto restart**   | ✅ Yes (by kubelet)             |
| 👀 **Visible in API** | 🪞 Mirror Pod                  |

 ## 🔹 What is a Mirror Pod❓

   * Even though static pods are local, kubelet creates a mirror pod in the `API server`.
   * 👉 You can see it using: `kubectl get pods`
   * 📌 But you cannot `edit/delete` it using `kubectl`

## What happens if you delete a Static Pod using kubectl❓
 
  * 👉 It gets recreated automatically, Because the YAML file still exists on the node
  * 👉 Delete the manifest file: `rm /etc/kubernetes/manifests/pod.yaml` , It delete the Static Pod permanently

## Can you expose a Static Pod using a Service❓

  * Yes — if it has `labels`

## If API server is down, will Static Pods still run❓

 * YES, 👉 Because `kubelet` manages them locally (That’s why control plane runs as Static Pods)
 
 ## 🔥 Real-World Use Cases

  * In clusters (like `kubeadm`), control plane components run as static pods : `API Server`, `Scheduler`, `Controller Manager`, `etcd`
     * 👉 Because they don’t depend on the `API server`
     * 👉 Ensures cluster can `self-start` (bootstrapping)

## ⚠️ Key Truth (Interview Highlight)
  * 👉 In Amazon EKS, you `DO NOT manage control plane nodes`
  * 👉 So you cannot access `/etc/kubernetes/manifests` on control plane
  * ❌ Meaning : You cannot create or modify Static Pods for control plane components in EKS
  * 👉 You can create Static Pods manually on `worker nodes`
      * ❌ Not supported on: `AWS Fargate` (no node access), Control plane nodes (fully managed)
      * ✅ Only works on: `EC2 worker nodes`

---

 * ⚡ One-line (Interview 🔥)
   * 👉 Static Pod = Kubelet-managed 📄 + No scheduler ❌ + One Node-specific 📍

---

## ⚡ Static Pods in Kubernetes — Rapid Fire Q&A

| 🔢 Q#   | ❓ Question                              | 💡 Answer                                                                                                                                                                  |
| ------- | ----------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 🔹 Q1   | What is a Static Pod?                     | 👉 A pod managed directly by the `kubelet` on a node (`not by the API server`).                                                                                                |
| 🔹 Q2   | Who creates Static Pods?                  | 👉 `The kubelet`.                                                                                                                                                            |
| 🔹 Q3   | Where are Static Pod definitions stored?  | 👉 On the `node filesystem` (`not in etcd`).                                                                                                                                   |
| 🧠 Q4   | How does kubelet know about Static Pods?  | 👉 Via the `--pod-manifest-path` (or staticPodPath) directory.                                                                                                               |
| 🧠 Q5   | Example path?                             | 👉 `/etc/kubernetes/manifests `                                                                                                                                              |
| ⚙️ Q6   | What happens if a Static Pod crashes?     | 👉 Kubelet automatically `restarts `it.                                                                                                                                      |
| ⚙️ Q7   | What if you delete a Static Pod manually? | 👉 It gets recreated (because `manifest still exists`).                                                                                                                      |
| ⚙️ Q8   | Can Static Pods be scheduled?             | 👉 ❌ No — they are bound to a `specific node`.                                                                                                                               |
| 🔍 Q9   | What is a Mirror Pod?                     | 👉 A representation of `Static Pod in API server`.                                                                                                                           |
| 🔍 Q10  | Why Mirror Pods exist?                    | 👉 For visibility via kubectl.                                                                                                                                             |
| 🔍 Q11  | Can you edit Mirror Pods?                 | 👉 ❌ No — changes must be `made in manifest file`.                                                                                                                           |
| ⚖️ Q12  | Feature: Static Pod vs Normal Pod         | 👉 Managed by: kubelet vs API server; Stored in: Node filesystem vs etcd; Scheduler used: ❌ No vs ✅ Yes; Auto-recreated: ✅ Yes vs Depends on controller                    |
| 🚀 Q13  | Where are Static Pods commonly used?      | 👉 Control plane components: kube-apiserver, kube-scheduler, kube-controller-manager                                                                                       |
| 🚀 Q14  | Why Static Pods for control plane?        | 👉 Ensures components run even if API server is down.                                                                                                                      |
| 📄 Q15  | Example Static Pod YAML?                  | 👉 apiVersion: v1 <br> kind: Pod <br> metadata: name: static-nginx <br> spec: containers: - name: nginx image: nginx <br><br> Place this file in /etc/kubernetes/manifests |
| ⚠️ Q16  | Can Static Pods use ConfigMaps/Secrets?   | 👉 ❌ Not directly (limited support).                                                                                                                                       |
| ⚠️ Q17  | Can they be part of Deployment?           | 👉 ❌ No                                                                                                                                                                    |
| ⚠️ Q18  | Can they use service accounts?            | 👉 Limited / not recommended.                                                                                                                                              |
| 🛠️ Q19 | Static Pod not running?                   | 👉 Check: Manifest path, kubelet logs, YAML syntax                                                                                                                         |
| 🛠️ Q20 | Pod not visible in kubectl?               | 👉 Mirror pod may not be created.                                                                                                                                          |
| 🔄 Q21  | What is kubelet role here?                | 👉 Watches manifest directory & manages pods.                                                                                                                              |
| 🔄 Q22  | What happens if kubelet restarts?         | 👉 Static Pods are recreated automatically.                                                                                                                                |
| 🎯 Q23  | Why Static Pods used in kubeadm clusters? | 👉 For control plane bootstrapping.                                                                                                                                        |
| 🎯 Q24  | What if API server is down?               | 👉 Static Pods still run (managed by kubelet).                                                                                                                             |
| 🎯 Q25  | How to update Static Pod?                 | 👉 Modify manifest file → kubelet reloads.                                                                                                                                 |
| 🎯 Q26  | Can you scale Static Pods?                | 👉 ❌ No (manual per node only).                                                                                                                                            |
