# Labels in Kubernetes

## What are Labels?
Labels are **key-value pairs** attached to Kubernetes objects (like Pods, Services, Namespaces, etc.) used to identify, group, and select resources.

**Key-Value Format:** `key=value`  
Example: `app=frontend`

---

## Real-Life Analogy
- **Namespaces** are like different folders in your computer — each is separate.  
- **Labels** are like tags on files — you can add multiple tags to the same file for search/filtering.
- Labels are metadata used for ***grouping and selection,*** One resource can have multiple labels

---

## Example YAML
```yaml
metadata:
  labels:
    app: frontend
    tier: web
    environment: production
    version: "3.2"
    team: devops
```

---

## CLI Commands

### Add a label
```bash
kubectl label pods nginx environment=production
```

### Show labels for all pods
```bash
kubectl get pods --show-labels
```

### Filter by label
```bash
kubectl get pods -l app=frontend
```

### View specific labels
```bash
kubectl get pods -L app,env
```

### Update labels
```bash
kubectl label pods nginx env=staging --overwrite
```

### Remove labels
```bash
kubectl label pods nginx env-
```
