# ReplicaSet in Kubernetes

## Overview
A **ReplicaSet** is a Kubernetes controller that ensures a specified number of **Pod replicas** are always running.  
If a Pod crashes or is deleted, the ReplicaSet automatically creates a new one to maintain the desired count.

ReplicaSet is the improved version of **ReplicationController**.  
If the ReplicaSet selector and Pod labels don’t match, the ReplicaSet won’t control the Pods.

> **Best Practice:** Use **Deployments** instead of creating ReplicaSets directly, because Deployments manage ReplicaSets for rolling updates and rollbacks.

---

## Key Features
- Maintains pod count (scales up/down as needed)
- Works with Deployments for rolling updates
- Uses label selectors to identify managed pods
- Self-healing (replaces failed pods automatically)

---

## Best Practices
- Always use **Deployment** (which manages ReplicaSets automatically) for modern Kubernetes.
- Avoid **ReplicationController** (it is outdated).

---

## Example: Create a ReplicaSet

```yaml
apiVersion: apps/v1          # API version for ReplicaSet
kind: ReplicaSet             # Resource type
metadata:
  name: my-nginx-rs          # Name of the ReplicaSet

spec:
  replicas: 3                # Desired number of Pod replicas

  selector:                  # Selects Pods with matching labels
    matchLabels:
      app: nginx             # Matches Pods that have app=nginx

  template:                  # Template to create new Pods if needed
    metadata:
      labels:
        app: nginx           # Must match the selector labels above
    spec:                    # Pod specification
      containers:
      - name: nginx          # Container name
        image: nginx:1.21    # NGINX container image version 1.21
