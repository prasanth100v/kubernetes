# 🎛️ ConfigMaps in Kubernetes --- Simplified Guide

## 📘 What is a ConfigMap?

A **ConfigMap** is a Kubernetes object used to store **non‑sensitive
configuration data** separately from your application code.

It stores **key‑value pairs** such as: - Environment variables -
Application settings - Configuration files

Pods can consume ConfigMaps in three ways: - 🌿 Environment Variables -
🖥️ Command-line arguments - 📁 Mounted configuration files

> ⚠️ Maximum size of a ConfigMap is **1MB**.

------------------------------------------------------------------------

# 🔧 Why Use ConfigMaps?

✅ Separate configuration from application code\
✅ Update configuration **without rebuilding container images**\
✅ Make applications more **portable and flexible**

🚫 **Do NOT store sensitive data here.**\
Use **Kubernetes Secrets or Vault** instead.

------------------------------------------------------------------------

# 🧪 Example Use Cases

Typical values stored in ConfigMaps:

-   `LOG_LEVEL=info`
-   `APP_MODE=prod`
-   External API URLs
-   Database connection URLs *(non-secret)*

Example: Instead of hardcoding a database URL inside code, store it in a
**ConfigMap**.

------------------------------------------------------------------------

# 🧠 Pro Tips

⚡ ConfigMaps are **not encrypted**.

⚡ Updating a ConfigMap **does not automatically update running pods**.

Pods will see changes only if: - The pod is **restarted**, or - The
ConfigMap is **mounted as a volume** and the application reloads the
file.

------------------------------------------------------------------------

# 📦 Example ConfigMap

``` yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-configmap
data:
  USER: admin
  PORT: "8080"
  db.conf: |
    host=localhost
    port=3306
```

### 🔍 What this ConfigMap does

-   `USER` and `PORT` → injected as **environment variables**
-   `db.conf` → mounted as a **file inside the container**

Example location inside container:

    /etc/config/db.conf

------------------------------------------------------------------------

# 🔄 Do ConfigMaps Update Live?

| Usage Type \| Live Update \|

\|-------------\|-------------\| Environment Variables \| ❌ No \| \|
Mounted Volumes \| ✅ Yes \|

If used as **env variables**, pods must restart.

If used as **volumes**, files update automatically but the application
must reload them.

------------------------------------------------------------------------

# 🔐 ConfigMap vs Secret

  Feature     ConfigMap       Secret
  ----------- --------------- -------------------
  Data type   Non-sensitive   Sensitive
  Example     configs, URLs   passwords, tokens
  Encoding    Plain text      Base64 encoded

------------------------------------------------------------------------

# 🚀 Updating ConfigMap Without Downtime

Use **Rolling Updates with Deployments**.

Steps: 1. Update ConfigMap 2. Trigger deployment rollout 3. Kubernetes
replaces pods gradually

------------------------------------------------------------------------

# 🧩 Using ConfigMap in a Deployment

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      labels:
        app: demo
    spec:
      containers:
      - name: demo-container
        image: nginx

        envFrom:
        - configMapRef:
            name: my-configmap

        env:
        - name: ONLY_USER
          valueFrom:
            configMapKeyRef:
              name: my-configmap
              key: USER

        volumeMounts:
        - name: config-volume
          mountPath: /etc/config

      volumes:
      - name: config-volume
        configMap:
          name: my-configmap
```

------------------------------------------------------------------------

# 📌 Summary

✔ ConfigMaps store **non-sensitive configuration**\
✔ Separate **config from container images**\
✔ Can inject into pods as **env variables or files**\
✔ **Secrets should be used for sensitive data**

------------------------------------------------------------------------

⭐ **ConfigMaps make Kubernetes applications configurable, portable, and
easier to manage.**
