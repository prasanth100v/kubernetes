# 🔍 Kubernetes Probes Deep Dive (FailureThreshold + Real Example)

## ❓ What happens when a Liveness Probe fails?
👉 Kubernetes **💥kills the container and restarts it** 

- ✔ ⚙️ Behavior depends on restartPolicy
- ✔ Used for detecting crashed or stuck apps

#### 🔁 Restart Policy Impact
| restartPolicy    | Behavior                   |
| ---------------- | -------------------------- |
| Always (default) | 🔁 Always restarts         |
| OnFailure        | 🔁 Restart only on failure |
| Never            | ❌ No restart               |


---

## 🔐 What is failureThreshold?

👉 failureThreshold = Number of consecutive failures before Kubernetes takes action

---

## 📌 Applies to ALL Probes

### 🔹 Liveness Probe
- After failureThreshold failures → 🔄 Container restarted  

---

### 🔹 Readiness Probe
- After failureThreshold failures → 🚫 Pod removed from Service endpoints (no traffic)  
- ✔ Container NOT restarted  

---

### 🔹 Startup Probe
- After failureThreshold failures → ❌ Container is killed & restarted

---

## 🐢 Why Use Higher failureThreshold & periodSeconds?

👉 For slow or sensitive apps (Java, DB-heavy apps)

#### ⚠️ Problem:
Some apps are:
- 🐢 Slow to start (Java, Spring Boot)
- 🔌 Dependent on DB / APIs
- ⚡ Temporarily slow under load
          👉 Probes may fail temporarily, even if app is fine

Without tuning:
- App may restart unnecessarily  
- False failures  

### ✅ Solution:
| Setting             | Purpose                           |
| ------------------- | --------------------------------- |
| ⏱ periodSeconds     | Give more time between checks     |
| 🔢 failureThreshold | Allow more failures before action |

---

## ✅ Example
```
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 60
  periodSeconds: 30
  failureThreshold: 5
```
---

## 🧠 What This Means

- Wait 60 seconds before first check  
- Check every 30 seconds  
- Allow 5 failures  

👉 Total tolerance:
5 × 30 = **150 seconds (2.5 minutes)** before restart  ➡️ App gets 2.5 minutes to recover before restar

---

## ⚠️ Without Proper Settings

❌ Example:
- periodSeconds: 5  
- failureThreshold: 1  

👉 Result:
- App killed too early  
- CrashLoopBackOff  

---

# 🚀 Benefits of Kubernetes Probes

## 🔹 1. Improved Application Reliability
- 🔁 Self-healing → Liveness probe restarts failed containers  
- 🚀 Zero downtime deployments → Readiness prevents bad pods receiving traffic  

## 🔹 2. Better Traffic Management
- ⚖️ Smart load balancing → Only healthy pods receive traffic
- 🚫 Automatically removes unhealthy pods

## 🔹 3. Prevent Cascading Failures
- 🐢 Startup probe  → protects slow apps
- 🔌 Readiness → Stops traffic if dependencies fail (DB/cache)

## 🔹 4. Operational Visibility
```
kubectl get pods
```
👉 Shows:
- Running but not Ready → 🚦 Readiness failing  
- CrashLoopBackOff → ❌ Liveness failing

---

# # 🚀 Kubernetes Pod with All 3 Probes (Startup + Liveness + Readiness)
```
apiVersion: v1                 # 🔢 API version for Pod
kind: Pod                      # 📦 Resource type
metadata:
  name: myapp                  # 🏷️ Pod name
spec:
  containers:
    - name: myapp-container    # 📦 Container name
      image: myapp:latest      # 🐳 Container image
      ports:
        - containerPort: 8080  # 🌐 App runs on port 8080

                      ### 🚀 2. STARTUP PROBE (App Starting Check)
      startupProbe:
        httpGet:
          path: /startup       # 🔗 http://localhost:8080/startup
          port: 8080
        failureThreshold: 30   # ❌ Retry 30 times
        periodSeconds: 10      # ⏱️ Every 10 sec
                               # 👉 Max wait time = 30 × 10 = 300 sec (5 min)
                               # ⚠️ If still failing → container will be killed

                        ### ❤️ 3. LIVENESS PROBE (Is App Alive?)
      livenessProbe:
        httpGet:
          path: /health        # 🔗 http://localhost:8080/health
          port: 8080
        initialDelaySeconds: 15 # ⏳ Wait before first check
        periodSeconds: 20       # 🔁 Check every 20 sec
        failureThreshold: 3     # ❌ Restart after 3 failures
                                # 👉 Detects crashed or stuck apps

                       ### 🚦 4. READINESS PROBE (Ready for Traffic?)
      readinessProbe:          
        httpGet:
          path: /ready         # 🔗 http://localhost:8080/ready
          port: 8080
        initialDelaySeconds: 5 # ⏳ Wait before first check
        periodSeconds: 10      # 🔁 Check every 10 sec
        failureThreshold: 2    # ❌ Mark NotReady after 2 fails
                               # 👉 Pod removed from Service (no traffic)
```

---

# 🔄 Flow Summary

### 🟢 Step 1: Startup Probe
- Runs first
- ⛔ Blocks other probes
- ✅ If success → move to next stage
- ❌ If fails → container restarted

---

### 🔄 Step 2: Liveness Probe
- Runs continuously
- Checks app health 
- If fails → 💥 container restarted  

---

### 🚦 Step 3: Readiness Probe
- Controls traffic
- ❌ If fails → removes pod from service
- ✅ If passes → allows traffic 
- ✔ No restart  

---

# 🧠 Real-World Scenario

👉 App needs:
- Database connection  
- External APIs  

✔ Readiness ensures:
- Traffic only when fully ready  

---

# ⚠️ Best Practices

- ✅ Use startup probe for slow apps  
- ✅ Tune failureThreshold properly  
- ✅ Avoid aggressive checks  
- ✅ Always use readiness probe
- ✅ Keep endpoints lightweight

---

# 🧠 Quick Revision

- failureThreshold → Number of retries before action
- periodSeconds = Time between checks 
- Liveness → restart container  
- Readiness → stop traffic  
- Startup → protect slow apps  (Delay other probes)

---
### 🎯 Golden Rule
👉 Use all 3 probes together for production-grade applications:
- 🚀 Smooth startup
- 🔁 Self-healing
- 🚦 Intelligent traffic routing

# 🎯 One-Line Answer

failureThreshold defines how many probe failures Kubernetes tolerates before restarting a container or stopping traffic, helping avoid false failures in slow or unstable applications.
