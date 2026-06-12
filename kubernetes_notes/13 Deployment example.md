# 🚀 Kubernetes NGINX Deployment Example

```yaml
apiVersion: apps/v1                     # API version for Deployment
kind: Deployment                        # Resource type
metadata:
  name: nginx-deployment                # Deployment name
  labels:
    app: nginx                          # Label for Deployment

spec:
  replicas: 3                           # Number of desired Pods

  selector:
    matchLabels:
      app: nginx                        # Select Pods with label app=nginx

  strategy:
    type: RollingUpdate                 # Update strategy
    rollingUpdate:
      maxSurge: 1                       # Allow 1 extra Pod during update
      maxUnavailable: 1                 # Allow 1 Pod unavailable during update

  template:                             # Pod template
    metadata:
      labels:
        app: nginx                      # Pod label (must match selector)

    spec:
      containers:
      - name: nginx-container           # Container name
        image: nginx:1.25               # Container image version
        ports:
        - containerPort: 80             # Exposed container port

        resources:                      # Resource configuration
          requests:
            cpu: "100m"                 # Minimum CPU required
            memory: "128Mi"             # Minimum memory required
          limits:
            cpu: "200m"                 # Maximum CPU allowed
            memory: "256Mi"             # Maximum memory allowed

        livenessProbe:                  # Checks container health
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 10       # Wait before first check
          periodSeconds: 15             # Check interval

        readinessProbe:                 # Checks readiness for traffic
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5        # Delay before first check
          periodSeconds: 10             # Check interval
```

---

## 📊 Breakdown
  * 🔁 **replicas** – Number of Pods to run (3 Pods).
  * 🎯 **selector.matchLabels** – Deployment manages Pods having label `app: nginx`.
  * 📦 **template** – Defines Pod specification (image, ports, probes, resources).

### ⚙️ Resource Requests & Limits
   * The CPU and memory used by the Pod are allocated from the **Node’s available resources**.

---

## 🔍 Probes

### ✅ Readiness Probe
  * 📡 Checks whether the container is ready to `receive traffic`.
  * 🚫 If it fails, Kubernetes removes the Pod from `Service endpoints` (no traffic sent).
  * 🔄 Kubernetes **does not restart** the container.
  * **Behavior:**
     * After **5 seconds**, Kubernetes checks every **10 seconds** if the app is ready.

### ❤️ Liveness Probe
  * 💓 Checks whether the application is alive.
  * 🔁 If it fails, Kubernetes **restarts the container**.
  * **Behavior:**
    * After **10 seconds**, Kubernetes checks every **15 seconds**.

---

## 🌐 Port Mapping Example

```yaml
# Container
containers:
- name: my-app
  image: my-app
  ports:
  - containerPort: 8080
```

```yaml
# Service
spec:
  ports:
  - port: 80
    targetPort: 8080
```

## 🔄 **Traffic Flow:**

  - 🌍 **port**: 80 → Clients connect to the Service
  - 🎯 **targetPort**: 8080 → Service forwards traffic to this Pod port
  - 📦 **containerPort**: 8080 → App inside container listens here

| 🔢 **Step** | 🚀 **Flow**                     | 📖 **Explanation**                                                                     |
| ----------- | ------------------------------- | ---------------------------------------------------------------------------------------- |
| 1️⃣         | 🌍 Client → Service `port: 80`  | 👉 Entry point for users (e.g., browser connects here), User sends request to Service   |
| 2️⃣         | 🔗 Service → `targetPort: 8080` | 👉 Connects Service → Pod, Service forwards traffic to Pod                              |
| 3️⃣         | 📦 Pod → `containerPort: 8080`  | 👉 Application inside container handles request                                         |

👉 Client → Service **port 80** → Pod **targetPort 8080** → Container **containerPort 8080**

---

# Deployment vs Service Ports
### Deployment
```yaml
containers:
  - name: nginx
    image: nginx
    ports:
      - containerPort: 80
```
   * 👉 containerPort in Deployment is mainly `documentation/metadata`.
   * 🌍 It tells Kubernetes which port the application listens on `inside the container`., Kubernetes `does not use it for traffic routing`.

### Service 
```yaml
spec:
  ports:
  - port: 80
    targetPort: 80
```
  * `port` = Service port (what clients connect to).
  *` targetPort` = Port on the `Pod/container` where traffic is sent.

## Important Rule
  * ✅ `targetPort` must match the port where the `application is actually listening`.
  * ❌ If the application listens on `8080` but targetPort is `9090`, traffic will fail.

## Q: Is Service port 80 mandatory?
 * 🔗 No. Service port can be `any valid port number`. You can use any valid port number (`1–65535`) for the Service port.
 * Port` 80` is commonly used for `HTTP applications`, but Kubernetes allows any port.
 * 🌍 The `targetPort` should match the application's listening port inside the container.
