# 🔍 Kubernetes Probes (Startup, Liveness, Readiness) 

## 🌟 What are Probes in Kubernetes?
Probes are used to check the **health of containers** running inside Pods.

👉 Kubernetes uses probes to decide:

- 🔁 When to restart a container
- 🚦 When to send traffic
- ❌ When to stop routing traffic
- ⏳ Wait for app to start 

---

## 🎯 Why Probes are Important
Probes ensure your application:

1. 🟢 Starts properly → Startup Probe  
2. 🔄 Restarts if crashed → Liveness Probe  
3. 🚦 Receives traffic only when ready → Readiness Probe  

---

## 🧠 Kubernetes provides 3 types of probes:
| Probe Type         | Purpose                            |
| ------------------ | ---------------------------------- |
| 🟢 Startup Probe   | Checks if app has started          |
| ❌ Liveness Probe   | Checks if app is alive             |
| 🚦 Readiness Probe | Checks if app is ready for traffic |
 

---

# 🔹 1. Liveness Probe (Alive Check)

👉 Checks if the application is running properly

❌ If fails → Kubernetes RESTARTS the container

---

### 📌 Example
```
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10
```
---

### 🔍 Explanation:

- 🌐 httpGet → Calls → http://localhost:8080/health
- ⏱ initialDelaySeconds → Waits 5 seconds before first checking
- 🔁 periodSeconds → Check every 10
- ❌ If fails multiple times → container restarts

---

### 🎯 Use Case

- App stuck  
- Deadlock  
- Not responding  

---

# 🔹 2. Readiness Probe (Traffic Control)

👉 Checks if the container is ready to receive traffic

❌ If fails:
- Pod is removed from Service endpoints
- No traffic is sent  

✔ Container is NOT restarted  

#### ⚠️ Important:
- Container still runs
- Only traffic is stopped
---

### 📌 Example
```
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10
```
---

### 🧠 Explanation

- App may be running but:
  - DB not connected  🗄️
  - Cache not ready  ⚡
  - Config not loaded  ⚙️

#### ➡️ You DON'T want traffic yet 👉 Traffic is blocked until ready  

---

### 🎯 Use Case

- Wait for DB connection  
- Wait for external APIs  
- Warm-up cache  

---

# 🔹 3. Startup Probe (Slow Start Apps)

👉 Used for slow-starting applications (Java, Spring Boot, Large microservices 🚀)

👉 What it does:
- Checks if the application has started successfully
- ❌ If it fails: ➡️ Kubernetes kills and restarts container

---

### 📌 Example
```
startupProbe:
  httpGet:
    path: /startup
    port: 8080
  failureThreshold: 30
  periodSeconds: 10
```
---

### 🧠 Explanation

⏱ Behavior:
- ⏳  30 attempts × 10s = 300s (5 minutes) wait
- During this time:  Kubernetes waits before checking other probes
- ❌ Liveness disabled
- ❌ Readiness disabled
- 👉 Gives enough time for app to start  

---

### 🎯 Use Case

- Apps taking long time to boot  
- Heavy initialization  

---

# 🔁 How All Probes Work Together

- Startup Probe → App start check  
- Liveness Probe → Runtime health  
- Readiness Probe → Traffic control  

👉 Best practice: Use ALL three together  

---

# 🧪 Probe Types

## 🌐 HTTP Check (httpGet)
```
httpGet:
  path: /health
  port: 8080
```
👉 Calls HTTP endpoint  

---

## 🔌 TCP Check (tcpSocket)
```
tcpSocket:
  port: 3306
```
➡️ Checks if port is open (e.g., MySQL)

---

## 🖥 Command Check (exec)
```
exec:
  command: ["cat","/tmp/healthy"]
```
👉 Runs command inside container  

---

# 🧠 Key Parameters

- initialDelaySeconds → Delay before first check  
- periodSeconds → Interval between checks  
- successThreshold → Success count to mark healthy  
- failureThreshold → Failure count to mark unhealthy
- 🔍 httpGet / exec / tcpSocket → Type of probe

---
## 🎯 When to Use What?
| Situation                        | Use Probe            |
| -------------------------------- | -------------------- |
| App crashes                      | ❌ Liveness           |
| App not ready (DB/cache loading) | 🚦 Readiness         |
| App slow startup                 | 🟢 Startup           |
| Web server health check          | Liveness + Readiness |

# 🔥 What Happens on Failures?

### ❌ Liveness Probe Failure
👉 Container is RESTARTED  

---

### ❌ Readiness Probe Failure
👉 Pod removed from Service  
👉 No traffic  
👉 Container NOT restarted  

---

### ❌ Startup Probe Failure
👉 Container is KILLED and restarted  

---

# 🌍 Real-World Example
#### 💳 Payment Service:

Before ready, it must:
- Connect to DB 🗄️
- Connect to APIs 🌐
- Load configs ⚙️

➡️ Readiness probe ensures:
No traffic allow until everything is ready

---

# 🧪 Troubleshooting Probes

### 📌 Check Logs
```
kubectl logs <pod>
```
### 📌 Describe Pod
```
kubectl describe pod <pod>         #👉 Shows probe failures and events  
```
### 📌 Test Manually
```
kubectl exec -it <pod> -- curl http://localhost:8080/health
```

# 🔧 Useful kubectl Commands 
```
kubectl get pod <pod-name> -o yaml               # ✅ Check Probe Config
kubectl describe pod <pod-name>                 # ✅ Describe Pod
kubectl delete pod <pod-name>                    # ✅ Delete Pod (Testing Restart)
```
```
kubectl get events --watch                    # 🔁 Watch Real-Time Events
```
👉 Shows:
- Probe failures  
- Pod start/stop  
- Scheduling issues  

```
kubectl get pods --watch                    # 🔍 Watch Pod Status
```
👉 Shows:
- Pending → Running → Ready  
- CrashLoopBackOff  
- Errors  

---
# 🔀 Namespace Commands
```
kubectl get events -n my-namespace --watch  
kubectl get pods -n my-namespace --watch  
```

```
kubectl get pods -o wide --watch                  # 🔄 Extra Info
```

# 🎯 Interview Questions

### ❓ What happens if readiness probe fails?
👉 Pod removed from service (no traffic)  ➡️ No traffic, but container keeps running

---

### ❓ What happens if liveness probe fails?
👉 Container is restarted  

---

### ❓ Your app takes 90 seconds to start. What will you do?
👉 Use Startup Probe  
Example:
```
- failureThreshold: 30  
- periodSeconds: 3  
```
➡️ After startup success: Enable Liveness & Readiness
---

# ⚠️ Best Practices

- ✅ Always use readiness probe  
- ✅ Use startup probe for slow apps  
- ✅ Use liveness for health checks  
- ❌ Don’t rely only on liveness
- ✅ Combine all 3 probes for production
- ✅ Monitor probe failures regularly

---

# 🧾 Final Summary

- 🟢 Startup Probe → Handles slow startup
- ❌ Liveness Probe → Restarts unhealthy apps
- 🚦 Readiness Probe → Controls traffic

👉 Together, they ensure:
- ✔️ High availability
- ✔️ Zero downtime
- ✔️ Reliable deployments
---

## 🎯 One-Line Answer

Kubernetes probes monitor container health: startup ensures proper initialization, liveness restarts unhealthy containers, and readiness controls traffic flow.
