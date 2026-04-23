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
   * 👉 Static Pod = Kubelet-managed 📄 + No scheduler ❌ + Node-specific 📍
