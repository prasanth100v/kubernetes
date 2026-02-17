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
```
# ReplicaSet Troubleshooting and Commands

## Troubleshooting

- **Pods not created? Check:**
  - Selector matches pod labels
  - Resource quotas are not exceeded
  - Node availability is sufficient

- **Too many pods? Verify:**
  - No other controllers are managing the same pods
  - Correct replica count is set

- The labels in `template.metadata.labels` **must match** the `selector.matchLabels`.

- Do not forget:
  ```yaml
  apiVersion: apps/v1
  kind: ReplicaSet
  
 - ReplicaSet does not support rolling updates like Deployments.
 - If you update the image in the ReplicaSet YAML, existing pods will not automatically update.
 - When you manually delete the old pods, the ReplicaSet creates new ones with the updated image.

## ReplicaSet Commands
```bash
# Create a ReplicaSet from YAML
kubectl apply -f replicaset.yaml

# List all ReplicaSets
kubectl get rs

# Get detailed information including events and selector labels
kubectl describe rs <replicaset-name>

# Change the number of replicas
kubectl scale rs <replicaset-name> --replicas=5

# Delete a ReplicaSet
kubectl delete rs <replicaset-name>

# View Pods created by a ReplicaSet
kubectl get pods --selector=app=nginx

# List all pods along with their labels
kubectl get pods --show-labels

# Watch real-time updates of pods
kubectl get pods -w

# Edit a ReplicaSet
kubectl edit rs <replicaset-name>
```
