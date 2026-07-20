# 🚨 Kubernetes Error: ConfigMap Not Found
## 📌 What is a ConfigMap?
 * A ConfigMap stores non-sensitive configuration data (such as 𝙚𝙣𝙫𝙞𝙧𝙤𝙣𝙢𝙚𝙣𝙩 𝙫𝙖𝙧𝙞𝙖𝙗𝙡𝙚𝙨, 𝙖𝙥𝙥𝙡𝙞𝙘𝙖𝙩𝙞𝙤𝙣 𝙨𝙚𝙩𝙩𝙞𝙣𝙜𝙨, and 𝙘𝙤𝙣𝙛𝙞𝙜𝙪𝙧𝙖𝙩𝙞𝙤𝙣 𝙛𝙞𝙡𝙚𝙨) that pods can consume.
 * If a pod references a ConfigMap that does not exist, the pod may fail to start or remain in the 𝙋𝙚𝙣𝙙𝙞𝙣𝙜 or 𝘾𝙧𝙚𝙖𝙩𝙚𝘾𝙤𝙣𝙩𝙖𝙞𝙣𝙚𝙧𝘾𝙤𝙣𝙛𝙞𝙜𝙀𝙧𝙧𝙤𝙧 state.
 * Example STATUS: 1️⃣𝗖𝗿𝗲𝗮𝘁𝗲𝗖𝗼𝗻𝘁𝗮𝗶𝗻𝗲𝗿𝗖𝗼𝗻𝗳𝗶𝗴𝗘𝗿𝗿𝗼𝗿 or 2️⃣𝗘𝗿𝗿𝗼𝗿: 𝗰𝗼𝗻𝗳𝗶𝗴𝗺𝗮𝗽 "<𝗰𝗼𝗻𝗳𝗶𝗴𝗺𝗮𝗽-𝗻𝗮𝗺𝗲>" 𝗻𝗼𝘁 𝗳𝗼𝘂𝗻𝗱

## 1️⃣ Check Existing ConfigMaps
```hcl
kubectl get configmaps / kubectl get cm

---
NAME          DATA   AGE
app-config    2      10m                # If the required ConfigMap is missing, create it.
```

## 🚀 ConfigMap Not Found Troubleshooting Flow
```hcl
Pod Fails to Start
        ↓
kubectl describe pod <pod-name>
        ↓
Error: `ConfigMap Not Found`
        ↓
kubectl get configmaps
        ↓
ConfigMap Exists?
     ↓             ↓
    Yes            No
     ↓             ↓
Check Namespace   Create ConfigMap
     ↓             ↓
Verify Deployment Reference
        ↓
Restart Deployment
        ↓
Pod Starts Successfully ✅
```

## ☸️ Kubernetes ConfigMap Not Found — Common Reasons & Solutions
| 🚨 **Cause**                          | 📖 **Description**                                      | 🛠️ **Solution**                                                                     | 💻 **Useful Commands**                          |
| ------------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------------------------- | ----------------------------------------------- |
| 📝 **Incorrect ConfigMap Name**       | Deployment references the wrong ConfigMap name          | Update the Deployment/Pod YAML with the correct ConfigMap name                        | `kubectl describe pod <pod-name>`               |
| ❌ **ConfigMap Does Not Exist**       | ConfigMap was never created or has been deleted         | Create or recreate the ConfigMap and apply it                                         | `kubectl get configmap`                         |
| 📂 **Wrong Namespace**                | ConfigMap exists in another namespace                   | Create the ConfigMap in the correct namespace or deploy the Pod in the same namespace | `kubectl get configmap -A`                      |
| 🔤 **Typo in YAML**                   | ConfigMap name is misspelled                            | Correct the spelling in the Pod or Deployment manifest                                | `kubectl describe deployment <deployment-name>` |
| 🔄 **Configuration Drift**            | Deployment was updated but the ConfigMap wasn't applied | Reapply the updated ConfigMap and restart the Pods if necessary                       | `kubectl apply -f configmap.yaml`               |
| 📄 **Missing Key in ConfigMap**       | Required key is not present in the ConfigMap            | Add the missing key and update the ConfigMap                                          | `kubectl describe configmap <configmap-name>`   |
| 📦 **Incorrect Volume/Env Reference** | Pod references a non-existent ConfigMap key             | Verify `env`, `envFrom`, or `volumeMount` configuration                               | `kubectl describe pod <pod-name>`               |

## 🔍 Useful Troubleshooting Commands
| 💻 **Command**                                         | 🎯 **Purpose**                          |
| ------------------------------------------------------ | ---------------------------------------- |
| `kubectl get configmap`  /  `kubectl get cm`           | List ConfigMaps in the current namespace |
| `kubectl get configmap -A`                             | List ConfigMaps across all namespaces    |
| `kubectl describe configmap <configmap-name>`          | Inspect ConfigMap contents               |
| `kubectl describe pod <pod-name>`                      | View ConfigMap-related errors            |
| `kubectl get deployment <deployment-name> -o yaml`     | Verify ConfigMap references              |
| `kubectl apply -f configmap.yaml`                      | Create or update the ConfigMap           |
| `kubectl rollout restart deployment <deployment-name>` | Restart Pods after ConfigMap changes     |
| `kubectl create configmap <name> --from-literal=KEY=VALUE` | Create a ConfigMap                   |
| `kubectl logs <pod-name>`                                  | Check application startup logs       |
| `kubectl get events --sort-by=.metadata.creationTimestamp` | View recent cluster events           |

## 🎯 Interview Answer
### Q: How do you troubleshoot a "ConfigMap not found" error?
 * Run `kubectl describe pod <pod-name>` to identify the exact ConfigMap error.
 * Verify that the ConfigMap exists using `kubectl get configmap`.
 * Ensure the ConfigMap is in the same namespace as the Pod.
 * Check for spelling mistakes in the ConfigMap name.
 * Verify that all required keys exist in the ConfigMap.
 * Reapply the ConfigMap if it was updated, then restart the Deployment if required.

## 🎯 Interview One-Liner
 * ☸️ A ConfigMap not found error occurs when a Pod cannot locate the referenced ConfigMap due to an incorrect name, missing ConfigMap, wrong namespace, YAML typo, missing keys, or configuration drift.
 * 🚀 The first troubleshooting step is to inspect the Pod with `kubectl describe pod` and verify the ConfigMap using `kubectl get configmap`.
