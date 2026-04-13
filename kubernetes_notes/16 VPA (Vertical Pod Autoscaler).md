# 📈 What is VPA (Vertical Pod Autoscaler)?

 * Vertical Pod Autoscaler (VPA) automatically adjusts **CPU and memory (resource requests/limits)** for your Pods based on actual usage.
 * Instead of scaling the number of pods (like HPA), **VPA scales the size of the pods**.

## 🎯 Why Use VPA?
  - ⚠️ Helps avoid **OOM (Out of Memory)** errors or underperformance due to low resources.  
  - 💰 Prevents **overprovisioning** of CPU and memory.  
  - 🚀 Improves resource efficiency in Kubernetes clusters.

---

## 📦 Install VPA
**⚙️ Installing the Vertical Pod Autoscaler (VPA) into your Kubernetes cluster**
``` bash
kubectl apply -f https://github.com/kubernetes/autoscaler/releases/latest/download/vertical-pod-autoscaler.yaml
```

### ✅ Verify the VPA is Installed
``` bash
kubectl get pods -n kube-system | grep vpa
```

## 🧾 Sample VPA YAML (Auto Mode)
``` yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: vpa-example
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment  # The target workload
  updatePolicy:
    updateMode: "Auto"
```

---

## 📌 Important Notes

 - 🔄 VPA **restarts pods** when updating resource requests/limits.  
 - 📦 Works best with **Deployments** or **StatefulSets**.  
 - ⚖️ **HPA** scales pod count, while **VPA** `scales resource limits` per pod.

## 🚦 VPA Modes (Vertical Pod Autoscaler)
| 🧩 Mode    | 💡 Description                                            | 🧠 Simple Meaning                                  |
| ---------- | ---------------------------------------------------------- | -------------------------------------------------- |
| 🔍 Off     | 📊 Only suggests resource values                           | 🧠 “Tell me what to do, but don’t change anything” |
| 🎯 Initial | ⚙️ Sets resources at pod start only                        | 🛠️ “Set it correctly once when the pod starts.”    |
| 🤖 Auto    | 🔄 Updates resources full automatically (may restart pods) | ⚡ “Continuously adjust even if restart needed”     |

