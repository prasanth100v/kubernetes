# 💥Kubernetes Deployment Rolling Update Guide
## 1. Rolling Update (Default)
**How it works:** Gradually replaces old Pods with new ones  
**Benefit:** No downtime  
**Configuration:** `strategy.type: RollingUpdate`  
Controlled by:
- `maxSurge`
- `maxUnavailable`

**Best for:**
- Production environments requiring zero downtime
- Web and stateless applications

## 🔄 Kubernetes Deployment YAML with Rolling Update strategy:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 4
  selector:
    matchLabels:
      app: nginx
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1            # Extra pods allowed during update
      maxUnavailable: 1      # Max unavailable pods during update
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80
```

## Useful Commands

``` bash
kubectl apply -f deployment.yaml                                       # Apply deployment
kubectl get deployments
kubectl get pods
kubectl describe deployment nginx-deployment
kubectl scale deployment nginx-deployment --replicas=5
kubectl set image deployment nginx-deployment nginx=nginx:1.26         # Update image (Rolling Update happens automatically)
kubectl rollout undo deployment nginx-deployment                       # Rollback if needed
kubectl rollout status deployment <name>                               # Check rollout status
kubectl rollout history deployment <name>
kubectl get endpoints
```

------------------------------------------------------------------------

## 💥🔄 Rolling Update Example

A rolling update means Pods are updated one by one.\
Let's say you want to update nginx to version **1.26**.

Run:

``` bash
kubectl set image deployment/myapp-deployment nginx=nginx:1.26
```

### 📌 What happens?

-   Kubernetes creates a new ReplicaSet with nginx:1.26.
-   It gradually replaces old Pods with new ones.
-   This is called a rolling update (zero downtime).

Controlled by: - **maxSurge**: Extra pods allowed during update (default: 25%)
               - **maxUnavailable**: Max pods that can be unavailable (default: 25%)

------------------------------------------------------------------------

## ❗🔁 Rollback Example

If something goes wrong after the update:

``` bash
kubectl rollout undo deployment/myapp-deployment
```

Kubernetes rolls back to the previous working version.

------------------------------------------------------------------------

## What is the use of strategy in a Deployment?

The **strategy** field defines how updates should be applied:

-   **RollingUpdate (default)** -- updates Pods gradually
-   **Recreate** -- deletes old Pods before creating new ones (causes downtime)

------------------------------------------------------------------------

## 🔄 How Rolling Updates Work

Suppose you update the image from **nginx:1.21** to **nginx:1.23**.

Kubernetes will: 
-  1. Create a new ReplicaSet with the updated image.
   2. Gradually bring up new Pods.
   3. Slowly remove old Pods.
   4. Ensure a minimum number of Pods are always running to avoid downtime.

------------------------------------------------------------------------

