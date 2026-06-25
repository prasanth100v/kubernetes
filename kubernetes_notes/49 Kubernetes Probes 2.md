# 🔍 Kubernetes Probes Deep Dive
## What happens when a Liveness Probe fails❓
 * 👉 Kubernetes **💥kills the container and restarts it**
 * ⚙️ Behavior depends on `restartPolicy`
 * Used for detecting `crashed` or `stuck apps`

#### 🔁 Restart Policy Impact
| restartPolicy    | Behavior                   |
| ---------------- | -------------------------- |
| Always (default) | 🔁 Always restarts         |
| OnFailure        | 🔁 Restart only on failure |
| Never            | ❌ No restart               |

## 🔐 What is failureThreshold❓
 * 👉 failureThreshold = Number of consecutive failures before Kubernetes takes action
 * 📌 Applies to ALL Probes :
     * 🔹 Liveness Probe : After failureThreshold failures → 🔄 Container restarted
     * 🔹 Readiness Probe :
         * After failureThreshold failures → 🚫 Pod removed from Service endpoints (`no traffic`)
         * Container NOT restarted
     * 🔹 Startup Probe :
         * After failureThreshold failures → ❌ Container is killed & restarted

---

## 🐢 Why Use Higher failureThreshold & periodSeconds❓
 * 👉 For slow or sensitive apps (Java, DB-heavy apps)
 * ⚠️ Problem - Some apps are:
     * 🐢 Slow to start (`Java, Spring Boot`)
     * 🔌 Dependent on `DB / APIs`
     * ⚡ Temporarily slow under load : 👉 Probes may fail temporarily, even if app is fine
     * Without tuning:
         * App may restart unnecessarily
         * False failures

### ✅ Solution:
| ⚙️ Setting              | 💡 Purpose                           |
| ----------------------- | ------------------------------------ |
| ⏱ **periodSeconds**     | 🔄 Gives more time between checks    |
| 🔢 **failureThreshold** | ❗ Allows more failures before action |

## ✅ Example
```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 60
  periodSeconds: 30
  failureThreshold: 5
```
## 🧠 What This Means
   - Wait `60 seconds` before first check  
   - Check every `30 seconds ` 
   - Allow `5 `failures

* 👉 Total tolerance:
     * 5 × 30 = **150 seconds (2.5 minutes)** before restart
     * ➡️ App gets 2.5 minutes to recover before restar

## ⚠️ Without Proper Settings
 * ❌ Example:
    - periodSeconds: 5  
    - failureThreshold: 1  
 * 👉 Result:
    - App killed too early  
    - CrashLoopBackOff  

## 🚀 Benefits of Kubernetes Probes
| 🔢 Category | 🌟 Benefit                               | 📖 Description                                                                                                                 |
| ----------- | ----------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| 🔹 1        | 🚀 **Improved Application Reliability** | 🔁 Liveness restarts failed containers (self-healing)<br>🚦 Readiness ensures zero-downtime deployments by blocking unhealthy pods receiving traffic |
| 🔹 2        | ⚖️ **Better Traffic Management**        | 👉 Only healthy pods receive traffic (smart load balancing)<br>🚫 Automatically removes unhealthy pods from service              |
| 🔹 3        | 🛡️ **Prevent Cascading Failures**       | 🐢 Startup probe protects slow-starting apps<br>🔌 Readiness blocks traffic if dependencies fail                                |
| 🔹 4        | 📊 **Operational Visibility**           | 👉 `kubectl get pods`  helps monitor status<br>🚦 Running but not Ready → Readiness failing<br>❌ CrashLoopBackOff = Liveness failing |

---

## 🚀 Kubernetes Pod with All 3 Probes (Startup + Liveness + Readiness)
```yaml
apiVersion: v1                           # 🔢 API version for Pod
kind: Pod                                # 📦 Resource type
metadata:
  name: myapp                            # 🏷️ Pod name
spec:
  containers:
    - name: myapp-container              # 📦 Container name
      image: myapp:latest                # 🐳 Container image
      ports:
        - containerPort: 8080            # 🌐 App runs on port 8080

                                  ### 🚀 2. STARTUP PROBE (App Starting Check)
      startupProbe:
        httpGet:
          path: /startup                 # 🔗 http://localhost:8080/startup
          port: 8080
        failureThreshold: 30             # ❌ Retry 30 times
        periodSeconds: 10                # ⏱️ Every 10 sec
                                         # 👉 Max wait time = 30 × 10 = 300 sec (5 min)
                                         # ⚠️ If still failing → container will be killed

                                    ### ❤️ 3. LIVENESS PROBE (Is App Alive?)
      livenessProbe:
        httpGet:
          path: /health                   # 🔗 http://localhost:8080/health
          port: 8080
        initialDelaySeconds: 15           # ⏳ Wait before first check
        periodSeconds: 20                 # 🔁 Check every 20 sec
        failureThreshold: 3               # ❌ Restart after 3 failures
                                          # 👉 Detects crashed or stuck apps

                                      ### 🚦 4. READINESS PROBE (Ready for Traffic?)
      readinessProbe:          
        httpGet:
          path: /ready                      # 🔗 http://localhost:8080/ready
          port: 8080
        initialDelaySeconds: 5              # ⏳ Wait before first check
        periodSeconds: 10                   # 🔁 Check every 10 sec
        failureThreshold: 2                 # ❌ Mark NotReady after 2 fails
                                            # 👉 Pod removed from Service (no traffic)
```

## 🔄 Flow Summary

### 🟢 Step 1: Startup Probe
 - Runs first
     - ⛔ Blocks other probes
     - ✅ If success → move to next stage
     - ❌ If fails → `container restarted`

### 🔄 Step 2: Liveness Probe
   - Runs continuously
   - Checks app health 
   - If fails → 💥 `container restarted  `

### 🚦 Step 3: Readiness Probe
   - Controls traffic
   - ❌ If fails → removes pod from service
   - ✅ If passes → `allows traffic` 
   - ✔ No restart  

## 🧠 Real-World Scenario
* 👉 App needs:
     - Database connection  
     - External APIs
     - ✔ Readiness ensures:
          - Traffic only when fully ready
       
## ⚠️ Best Practices
  - ✅ Use startup probe for slow apps  
  - ✅ Tune failureThreshold properly  
  - ✅ Avoid aggressive checks  
  - ✅ Always use readiness probe
  - ✅ Keep endpoints lightweight

## 🧠 Quick Revision
| 🧩 Concept              | 💡 Meaning                                  |
| ----------------------- | ------------------------------------------- |
| 🔢 **failureThreshold** | ❗ Number of retries before action           |
| ⏱ **periodSeconds**     | 🔄 Time between health checks               |
| ❌ **Liveness**          | 🔁 Restarts container if unhealthy          |
| 🚦 **Readiness**        | 🚫 Stops traffic to unhealthy Pod           |
| 🟢 **Startup**          | 🐢 Protects slow apps (delays other probes) |


### 🎯 Golden Rule
 * 👉 Use all 3 probes together for production-grade applications:
    * 🚀 Smooth startup
    * 🔁 Self-healing
    * 🚦 Intelligent traffic routing

## 🎯 One-Line Answer
   * failureThreshold defines how many probe failures Kubernetes tolerates before `restarting a container` or `stopping traffic`, helping avoid false failures in slow or unstable applications.

---

### 🚀 Kubernetes Probes (Liveness, Readiness & Startup) – Rapid Fire Interview Questions & Answers
| 🔢  | ❓ Question                                         | ✅ Answer                                                             |
| --- | -------------------------------------------------- | -------------------------------------------------------------------- |
| 1️⃣ | What is a Probe in Kubernetes?                     | 🩺 A health check used by Kubernetes to monitor container status.    |
| 2️⃣ | Why are Probes used?                               | 🚀 To ensure applications are healthy, ready, and started correctly. |
| 3️⃣ | How many types of Probes are there?                | 🎯 Three: Liveness, Readiness, and Startup.                          |
| 5️⃣ | Which component performs Probe checks?             | 🤖 Kubelet                                                           |
| 6️⃣ | Where are Probes configured?                       | 📄 Inside the Pod or Deployment YAML.                                |
| 7️⃣ | Can a container have multiple Probes?              | ✅ Yes                                                                |
| 8️⃣ | Are Probes mandatory?                              | ❌ No, but highly recommended.                                        |
| 9️⃣ | Which resource supports Probes?                    | 📦 Containers inside Pods.                                           |
| 🔟 | Do Probes check application health or node health? | 🩺 Application health.                                               |
| 1️⃣1️⃣ | What is a Liveness Probe?                 | ❤️ Checks if the application is still running properly.      |
| 1️⃣2️⃣ | What happens if the Liveness Probe fails? | 🔄 Kubernetes restarts the container.                        |
| 1️⃣3️⃣ | Purpose of Liveness Probe?                | 🛠️ Detect and recover from hung or deadlocked applications. |
| 1️⃣4️⃣ | Does Liveness control traffic?            | ❌ No                                                         |
| 1️⃣5️⃣ | Does Liveness restart the Pod?            | ❌ It restarts the **container** inside the Pod.              |
| 1️⃣6️⃣ | When should Liveness be used?             | 💥 When an application may freeze or become unresponsive.    |
| 2️⃣0️⃣ | Real-world example?                       | 🌐 Restart a web server if it hangs.                         |
| 2️⃣1️⃣ | What is a Readiness Probe?            | 🚦 Checks if the application is ready to receive traffic.               |
| 2️⃣2️⃣ | What happens if Readiness fails?      | 🚫 Pod is removed from the Service endpoints.                           |
| 2️⃣3️⃣ | Does Readiness restart the container? | ❌ No                                                                    |
| 2️⃣4️⃣ | Purpose of Readiness Probe?           | 🎯 Ensure only ready Pods receive traffic.                              |
| 2️⃣5️⃣ | When should Readiness be used?        | ⏳ Applications that need time to initialize or connect to dependencies. |
| 2️⃣6️⃣ | If Readiness succeeds?                | ✅ Pod receives traffic.                                                 |
| 2️⃣7️⃣ | If Readiness fails?                   | 🚫 Pod stays running but receives no traffic.                           |
| 2️⃣8️⃣ | Which object uses Readiness status?   | 🌐 Service                                                              |
| 2️⃣9️⃣ | Can Readiness recover automatically?  | ✅ Yes, when the probe succeeds again.                                   |
| 3️⃣0️⃣ | Real-world example?                   | 🗄️ Backend waits until database connection is established.             |
| 3️⃣1️⃣ | What is a Startup Probe?                             | 🚀 Checks whether the application has started successfully.  |
| 3️⃣2️⃣ | Why use Startup Probe?                               | 🕒 To avoid restarting slow-starting applications too early. |
| 3️⃣3️⃣ | What happens if Startup Probe fails?                 | 🔄 Kubernetes restarts the container.                        |
| 3️⃣4️⃣ | When does Startup Probe stop running?                | ✅ After the application starts successfully.                 |
| 3️⃣5️⃣ | What happens after Startup succeeds?                 | ❤️ Liveness and 🚦 Readiness Probes begin.                   |
| 3️⃣6️⃣ | Should Startup Probe be used for fast-starting apps? | ❌ Usually not needed.                                        |
| 3️⃣7️⃣ | Ideal use case?                                      | ☕ Java, Spring Boot, or large enterprise applications.       |
| 4️⃣1️⃣ | What methods can Probes use?                            | 🌐 HTTP, 🖥️ TCP, 💻 Exec                                                    |
| 4️⃣2️⃣ | What is an HTTP Probe?                                  | Sends an HTTP request to check application health.                           |
| 4️⃣3️⃣ | What is a TCP Probe?                                    | Checks whether a TCP port is open.                                           |
| 4️⃣4️⃣ | What is an Exec Probe?                                  | Runs a command inside the container.                                         |
| 4️⃣5️⃣ | What is `initialDelaySeconds`?                          | ⏳ Wait time before the first probe.                                          |
| 4️⃣6️⃣ | What is `periodSeconds`?                                | 🔄 Time between probe checks.                                                |
| 4️⃣7️⃣ | What is `timeoutSeconds`?                               | ⏱️ Maximum time to wait for a probe response.                                |
| 4️⃣8️⃣ | What is `failureThreshold`?                             | ❌ Number of failures before the probe is considered failed.                  |
| 4️⃣9️⃣ | What is `successThreshold`?                             | ✅ Number of successful checks before marking success (mainly for Readiness). |
| 5️⃣0️⃣ | Which Probe supports `successThreshold` greater than 1? | 🚦 Readiness Probe                                                           |

## 🎯 One-Line Interview Answer
 * 🩺 Kubernetes Probes monitor `application health`.
   * ❤️ Liveness Probe `restarts` unhealthy containers,
   * 🚦 Readiness Probe determines when a Pod can `receive traffic`, and
   * 🚀 Startup Probe ensures `slow-starting applications` are fully initialized before Liveness and Readiness checks begin.

