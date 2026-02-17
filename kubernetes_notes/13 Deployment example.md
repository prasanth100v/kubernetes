# Kubernetes NGINX Deployment Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1

  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-container
        image: nginx:1.25
        ports:
        - containerPort: 80

        resources:
          requests:
            cpu: "100m"
            memory: "128Mi"
          limits:
            cpu: "200m"
            memory: "256Mi"

        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 15

        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 10
```

---

## Breakdown

* **replicas** – Number of Pods to run (3 Pods).
* **selector.matchLabels** – Deployment manages Pods having label `app: nginx`.
* **template** – Defines Pod specification (image, ports, probes, resources).

### Resource Requests & Limits

The CPU and memory used by the Pod are allocated from the **Node’s available resources**.

---

## Probes

### Readiness Probe

* Checks whether the container is ready to receive traffic.
* If it fails, Kubernetes removes the Pod from Service endpoints (no traffic sent).
* Kubernetes **does not restart** the container.

**Behavior:**
After **5 seconds**, Kubernetes checks every **10 seconds** if the app is ready.

### Liveness Probe

* Checks whether the application is alive.
* If it fails, Kubernetes **restarts the container**.

**Behavior:**
After **10 seconds**, Kubernetes checks every **15 seconds**.

---

## Port Mapping Example

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

**Traffic Flow:**

Client → Service **port 80** → Pod **targetPort 8080** → Container **containerPort 8080**
