# Kubernetes Deployment Rolling Update Guide

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

Controlled by: - **maxSurge**: Extra pods allowed during update
(default: 25%) - **maxUnavailable**: Max pods that can be unavailable
(default: 25%)

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
-   **Recreate** -- deletes old Pods before creating new ones (causes
    downtime)

------------------------------------------------------------------------

## 🔄 How Rolling Updates Work

Suppose you update the image from **nginx:1.21** to **nginx:1.23**.

Kubernetes will: 1. Create a new ReplicaSet with the updated image. 2.
Gradually bring up new Pods. 3. Slowly remove old Pods. 4. Ensure a
minimum number of Pods are always running to avoid downtime.

------------------------------------------------------------------------

## Useful Commands

``` bash
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl get pods
kubectl describe deployment nginx-deployment
kubectl scale deployment nginx-deployment --replicas=5
kubectl set image deployment nginx-deployment nginx=nginx:1.23
kubectl rollout undo deployment nginx-deployment
kubectl rollout status deployment <name>
kubectl rollout history deployment <name>
kubectl get endpoints
```
