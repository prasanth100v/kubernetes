# 🔍 Kubernetes Probes (Startup, Liveness, Readiness) – Easy + Complete Guide

---

## 🌟 What are Probes in Kubernetes?

Probes are used to check the **health of containers** running inside Pods.

👉 Kubernetes uses probes to decide:
- Restart container 🔄
- Stop sending traffic 🚫
- Wait for app to start ⏳

---

## 🎯 Why Probes are Important

Probes ensure your application:

1. 🟢 Starts properly → Startup Probe  
2. 🔄 Restarts if crashed → Liveness Probe  
3. 🚦 Receives traffic only when ready → Readiness Probe  

---

## 🧠 Types of Probes

1. 🔹 startupProbe → App started or not  
2. 🔹 livenessProbe → App alive or stuck  
3. 🔹 readinessProbe → Ready to serve traffic  

---

# 🔹 1. Liveness Probe (Alive Check)

👉 Checks if the application is running properly

❌ If fails → Kubernetes RESTARTS the container

---

### 📌 Example

livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10

---

### 🧠 Explanation

- httpGet → Calls http://localhost:8080/health  
- initialDelaySeconds → Wait 5 seconds before checking  
- periodSeconds → Check every 10 seconds  

👉 If it fails multiple times → container restarts  

---

### 🎯 Use Case

- App stuck  
- Deadlock  
- Not responding  

---

# 🔹 2. Readiness Probe (Traffic Control)

👉 Checks if the app is READY to serve traffic

❌ If fails:
- Pod is removed from Service  
- No traffic is sent  

✔ Container is NOT restarted  

---

### 📌 Example

readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10

---

### 🧠 Explanation

- App may be running but:
  - DB not connected  
  - Cache not ready  
  - Config not loaded  

👉 Traffic is blocked until ready  

---

### 🎯 Use Case

- Wait for DB connection  
- Wait for external APIs  
- Warm-up cache  

---

# 🔹 3. Startup Probe (Slow Start Apps)

👉 Used for slow-starting applications (Java, Spring Boot)

❌ If fails → Container is restarted  

---

### 📌 Example

startupProbe:
  httpGet:
    path: /startup
    port: 8080
  failureThreshold: 30
  periodSeconds: 10

---

### 🧠 Explanation

- 30 attempts × 10s = 300s (5 minutes)  
- Kubernetes waits before checking other probes  

👉 Gives enough time for app to start  

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

## 🌐 httpGet

httpGet:
  path: /health
  port: 8080

👉 Calls HTTP endpoint  

---

## 🔌 tcpSocket

tcpSocket:
  port: 3306

👉 Checks if port is open  

---

## 🖥 exec

exec:
  command: ["cat","/tmp/healthy"]

👉 Runs command inside container  

---

# 🧠 Key Parameters

- initialDelaySeconds → Delay before first check  
- periodSeconds → Interval between checks  
- successThreshold → Success count to mark healthy  
- failureThreshold → Failure count to mark unhealthy  

---

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

# 🧠 Real-Time Scenario

👉 Payment Service:

- Needs DB connection  
- Needs API access  

✔ Readiness Probe ensures:
- Traffic only after everything is ready  

---

# 🧪 Troubleshooting Probes

### 📌 Check Logs
kubectl logs <pod>

---

### 📌 Describe Pod
kubectl describe pod <pod>

👉 Shows probe failures and events  

---

### 📌 Test Manually
kubectl exec -it <pod> -- curl http://localhost:8080/health

---

# 🔧 Useful kubectl Commands

### ✅ Check Probe Config
kubectl get pod <pod-name> -o yaml

---

### ✅ Describe Pod
kubectl describe pod <pod-name>

---

### ✅ Delete Pod (Testing Restart)
kubectl delete pod <pod-name>

---

# 🔁 Watch Real-Time Events

kubectl get events --watch

👉 Shows:
- Probe failures  
- Pod start/stop  
- Scheduling issues  

---

# 🔍 Watch Pod Status

kubectl get pods --watch

👉 Shows:
- Pending → Running → Ready  
- CrashLoopBackOff  
- Errors  

---

# 🔀 Namespace Commands

kubectl get events -n my-namespace --watch  
kubectl get pods -n my-namespace --watch  

---

# 🔄 Extra Info

kubectl get pods -o wide --watch

---

# 🎯 Interview Questions

### ❓ What happens if readiness probe fails?
👉 Pod removed from service (no traffic)

---

### ❓ What happens if liveness probe fails?
👉 Container is restarted  

---

### ❓ App takes 90 seconds to start?
👉 Use Startup Probe  
Example:
- failureThreshold: 30  
- periodSeconds: 3  

---

# ⚠️ Best Practices

- ✅ Always use readiness probe  
- ✅ Use startup probe for slow apps  
- ✅ Use liveness for health checks  
- ❌ Don’t rely only on liveness  

---

# 🧠 Quick Revision

- Startup → App start  
- Liveness → App health  
- Readiness → Traffic control  

---

# 🎯 One-Line Answer

Kubernetes probes monitor container health: startup ensures proper initialization, liveness restarts unhealthy containers, and readiness controls traffic flow.
