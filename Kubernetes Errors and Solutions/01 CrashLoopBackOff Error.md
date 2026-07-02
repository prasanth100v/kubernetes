# 🚨 Kubernetes Error 1: CrashLoopBackOff
### What is CrashLoopBackOff?
 * A CrashLoopBackOff error occurs when a container `starts`, `crashes`, Kubernetes `restarts it`, and the cycle repeats continuously.
 * Kubernetes gradually increases the wait time between restart attempts (BackOff).

### Common Causes
 * ❌ Application configuration errors
 * ❌ Missing environment variables
 * ❌ Incorrect startup command or entrypoint
 * ❌ Missing dependencies (Database, API, ConfigMap, Secret)
 * ❌ Invalid container image
 * ❌ Application crashes due to code issues
 * ❌ Port conflicts or permission issues

## Step 1: Check Pod Status
```hcl
kubectl get pods

NAME                        READY   STATUS             RESTARTS   AGE
myapp-7f9c6d9b8c-abcde      0/1     CrashLoopBackOff   5          10m
```

### Troubleshooting Flow
```hcl
CrashLoopBackOff
        │
        ▼
kubectl logs <pod-name>
        │
        ▼
kubectl describe pod <pod-name>
        │
        ▼
Check:
✔ Application logs
✔ Environment variables
✔ Startup command
✔ Container image
✔ ConfigMap / Secret
✔ Database connectivity
✔ Resource limits
        │
        ▼
Fix the issue
        │
        ▼
kubectl rollout restart deployment <deployment-name>
        │
        ▼
Pod Running Successfully ✅
```

### Frequently Used Commands
```hcl
kubectl get pods                                  # Check pod status
kubectl logs <pod-name>                           # View logs
kubectl logs <pod-name> --previous                # View previous logs
kubectl describe pod <pod-name>                          # Describe pod
kubectl edit deployment <deployment-name>                # Edit deployment
kubectl rollout restart deployment <deployment-name>     # Restart deployment
kubectl get pods -w                                      # Watch pod status
```

