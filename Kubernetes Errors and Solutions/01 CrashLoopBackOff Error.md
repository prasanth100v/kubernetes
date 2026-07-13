# ☸️ Kubernetes Error 1: CrashLoopBackOff
### 🚨 What is CrashLoopBackOff?
 * A CrashLoopBackOff error occurs when a container `starts`, `crashes`, and Kubernetes repeatedly tries to `restart` it.
 * After several failed attempts, Kubernetes `delays the restart`, resulting in the `CrashLoopBackOff` status.
 * 🚀 Common causes include 𝙖𝙥𝙥𝙡𝙞𝙘𝙖𝙩𝙞𝙤𝙣 𝙘𝙧𝙖𝙨𝙝𝙚𝙨, 𝙘𝙤𝙣𝙛𝙞𝙜𝙪𝙧𝙖𝙩𝙞𝙤𝙣 𝙚𝙧𝙧𝙤𝙧𝙨, 𝙢𝙞𝙨𝙨𝙞𝙣𝙜 𝙎𝙚𝙘𝙧𝙚𝙩𝙨, 𝙙𝙚𝙥𝙚𝙣𝙙𝙚𝙣𝙘𝙮 𝙛𝙖𝙞𝙡𝙪𝙧𝙚𝙨, 𝙧𝙚𝙨𝙤𝙪𝙧𝙘𝙚 𝙡𝙞𝙢𝙞𝙩𝙨, 𝙥𝙧𝙤𝙗𝙚 𝙛𝙖𝙞𝙡𝙪𝙧𝙚𝙨, and 𝙨𝙩𝙤𝙧𝙖𝙜𝙚 or 𝙥𝙚𝙧𝙢𝙞𝙨𝙨𝙞𝙤𝙣 𝙞𝙨𝙨𝙪𝙚𝙨.

## ☸️ Kubernetes CrashLoopBackOff — Common Reasons & Solutions
| 🚨 **Reason**                         | 📖 **Description**                                             | 🛠️ **Solution**                                                   | 💡 **Interview Tip**                   |
| ------------------------------------- | -------------------------------------------------------------- | ------------------------------------------------------------------ | -------------------------------------- |
| 💥 **Application Crash**              | Application throws an exception and exits immediately          | Check logs using `kubectl logs <pod-name>` and fix the application | Most common cause                      |
| ⚙️ **Incorrect Startup Command**      | Invalid `command` or `args`                                    | Verify `command` and `args` in the Deployment                      | Check the container entrypoint         |
| 🌱 **Missing Environment Variables**  | Required environment variables are missing                     | Add missing variables under `env`                                  | Verify ConfigMaps and Secrets          |
| ⚙️ **Configuration Errors**           | Invalid ConfigMap or configuration files                       | Validate ConfigMaps and mounted files                              | Check mounted configuration            |
| 🔐 **Missing Secrets**                | Secret is missing or incorrect                                 | Create/update the Secret and verify references                     | Confirm secret names and keys          |
| 🗄️ **Database Connection Failure**   | Application cannot connect to the database                     | Verify hostname, credentials, Service, and network                 | Ensure the database is reachable       |
| 🐳 **Image Issues**                   | Wrong image or outdated version                                | Verify image name/tag and redeploy                                 | Confirm the correct image is available |
| 🔗 **Application Dependency Failure** | Redis, Kafka, external API, etc. unavailable                   | Ensure dependent services are running                              | Verify connectivity to dependencies    |
| 💾 **OOMKilled**                      | Container exceeds its memory limit                             | Increase memory limits or optimize the application                 | Check Pod status for `OOMKilled`       |
| ⚡ **CPU Resource Limits**             | Insufficient CPU resources                                     | Increase CPU requests and limits                                   | Monitor CPU usage                      |
| ❤️ **Liveness Probe Failure**         | Kubernetes restarts the container because it appears unhealthy | Verify probe path, port, and timings                               | Ensure the health endpoint is correct  |
| 🩺 **Startup Probe Failure**          | Slow-starting application fails during initialization          | Increase `failureThreshold` or `initialDelaySeconds`               | Recommended for Java/Spring Boot apps  |
| 📂 **Read-only File System**          | Application writes to a read-only path                         | Mount a writable volume or change the write location               | Common with hardened containers        |
| 🚪 **Port Configuration Error**       | Application listens on a different port                        | Match the container port with the application port                 | Verify `containerPort`                 |
| 🔑 **Permission Issues**              | Application lacks file or directory permissions                | Fix file permissions, `securityContext`, or user                   | Common when running as a non-root user |
| 💽 **Volume Mount Failure**           | PVC, ConfigMap, or Secret isn't mounted correctly              | Verify PVC status and volume mounts                                | Check `kubectl describe pod`           |
| 🖥️ **Node-Level Issues**             | Node resource pressure or kubelet problems                     | Check node health using `kubectl describe node <node-name>`        | Review node events and conditions      |

---

## Troubleshooting Steps
### Check Pod Status
```hcl
kubectl get pods
---
NAME                        READY   STATUS             RESTARTS   AGE  
myapp-7f9c6d9b8c-abcde      0/1     ℂ𝕣𝕒𝕤𝕙𝕃𝕠𝕠𝕡𝔹𝕒𝕔𝕜𝕆𝕗𝕗   5         10m
```

### Troubleshooting Flow
```hcl
CrashLoopBackOff
        │
        ▼
kubectl logs <pod-name>
        │
        ▼
kubectl describe pod <pod-name>
        │
        ▼
Check:
✔ Application logs
✔ Environment variables
✔ Startup command
✔ Container image
✔ ConfigMap / Secret
✔ Database connectivity
✔ Resource limits
        │
        ▼
Fix the issue
        │
        ▼
kubectl rollout restart deployment <deployment-name>
        │
        ▼
Pod Running Successfully ✅
```

## Frequently Used Commands
```hcl
kubectl get pods                                  # Check pod status
kubectl logs <pod-name>                           # View Container Logs (Identify why the application is crashing.)
kubectl logs <pod-name> --previous                # View previous logs  (If the container has already restarted)
kubectl describe pod <pod-name>                          # Describe pod (View events and detailed pod information.)
kubectl edit deployment <deployment-name>                # Edit deployment (Missing environment variables are one of the most common reasons for application crashes.)
kubectl rollout restart deployment <deployment-name>     # Restart deployment
kubectl get pods -w                                      # Watch pod status
```

# 🎯 Interview Answer
𝗤: 𝗛𝗼𝘄 𝗱𝗼 𝘆𝗼𝘂 𝘁𝗿𝗼𝘂𝗯𝗹𝗲𝘀𝗵𝗼𝗼𝘁 𝗮 𝗖𝗿𝗮𝘀𝗵𝗟𝗼𝗼𝗽𝗕𝗮𝗰𝗸𝗢𝗳𝗳 𝗲𝗿𝗿𝗼𝗿❓ 

* 𝘾𝙝𝙚𝙘𝙠 𝙩𝙝𝙚 𝙋𝙤𝙙 𝙚𝙫𝙚𝙣𝙩𝙨 𝙪𝙨𝙞𝙣𝙜 𝙠𝙪𝙗𝙚𝙘𝙩𝙡 𝙙𝙚𝙨𝙘𝙧𝙞𝙗𝙚 𝙥𝙤𝙙.
* 𝙍𝙚𝙫𝙞𝙚𝙬 𝙩𝙝𝙚 𝙖𝙥𝙥𝙡𝙞𝙘𝙖𝙩𝙞𝙤𝙣 𝙡𝙤𝙜𝙨 𝙬𝙞𝙩𝙝 𝙠𝙪𝙗𝙚𝙘𝙩𝙡 𝙡𝙤𝙜𝙨 (𝙖𝙣𝙙 --𝙥𝙧𝙚𝙫𝙞𝙤𝙪𝙨 𝙞𝙛 𝙣𝙚𝙚𝙙𝙚𝙙).
* 𝙑𝙚𝙧𝙞𝙛𝙮 𝙩𝙝𝙚 𝙨𝙩𝙖𝙧𝙩𝙪𝙥 𝙘𝙤𝙢𝙢𝙖𝙣𝙙, 𝙚𝙣𝙫𝙞𝙧𝙤𝙣𝙢𝙚𝙣𝙩 𝙫𝙖𝙧𝙞𝙖𝙗𝙡𝙚𝙨, 𝘾𝙤𝙣𝙛𝙞𝙜𝙈𝙖𝙥𝙨, 𝙖𝙣𝙙 𝙎𝙚𝙘𝙧𝙚𝙩𝙨.
* 𝘾𝙝𝙚𝙘𝙠 𝙙𝙖𝙩𝙖𝙗𝙖𝙨𝙚 𝙖𝙣𝙙 𝙚𝙭𝙩𝙚𝙧𝙣𝙖𝙡 𝙨𝙚𝙧𝙫𝙞𝙘𝙚 𝙘𝙤𝙣𝙣𝙚𝙘𝙩𝙞𝙫𝙞𝙩𝙮.
* 𝙄𝙣𝙨𝙥𝙚𝙘𝙩 𝘾𝙋𝙐 𝙖𝙣𝙙 𝙢𝙚𝙢𝙤𝙧𝙮 𝙪𝙨𝙖𝙜𝙚, 𝙚𝙨𝙥𝙚𝙘𝙞𝙖𝙡𝙡𝙮 𝙛𝙤𝙧 𝙊𝙊𝙈𝙆𝙞𝙡𝙡𝙚𝙙.
* 𝙑𝙖𝙡𝙞𝙙𝙖𝙩𝙚 𝙡𝙞𝙫𝙚𝙣𝙚𝙨𝙨 𝙖𝙣𝙙 𝙨𝙩𝙖𝙧𝙩𝙪𝙥 𝙥𝙧𝙤𝙗𝙚 𝙘𝙤𝙣𝙛𝙞𝙜𝙪𝙧𝙖𝙩𝙞𝙤𝙣.
* 𝙑𝙚𝙧𝙞𝙛𝙮 𝙫𝙤𝙡𝙪𝙢𝙚 𝙢𝙤𝙪𝙣𝙩𝙨, 𝙛𝙞𝙡𝙚 𝙥𝙚𝙧𝙢𝙞𝙨𝙨𝙞𝙤𝙣𝙨, 𝙖𝙣𝙙 𝙣𝙤𝙙𝙚 𝙝𝙚𝙖𝙡𝙩𝙝.
