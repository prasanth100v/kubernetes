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


# 🌈 Kubernetes ConfigMap -- Usage Recap

## 🔍 3 Types of ConfigMap Usage

### 1️⃣ All Keys as Environment Variables (`envFrom`)

Inject all keys from a ConfigMap as environment variables.

    envFrom:
    - configMapRef:
        name: my-configmap

Example inside container:

    $USER=admin
    $PORT=8080

------------------------------------------------------------------------

### 2️⃣ Single Key as Environment Variable (`env + configMapKeyRef`)

Use a specific key from a ConfigMap as an environment variable.

    env:
    - name: MY_LOG_LEVEL
      valueFrom:
        configMapKeyRef:
          name: my-configmap
          key: log_level

Example:

    $MY_LOG_LEVEL=info

------------------------------------------------------------------------

### 3️⃣ Mount ConfigMap as Files (Volumes)

    volumeMounts:
    - name: config-volume
      mountPath: /etc/config

    volumes:
    - name: config-volume
      configMap:
        name: my-configmap

### 📂 Inside the Container

When the container starts:

    /etc/config/USER      → admin
    /etc/config/PORT      → 8080
    /etc/config/db.conf   →
    host=localhost
    port=3306

Each **ConfigMap key becomes a file**, and the **value becomes the file
content**.

------------------------------------------------------------------------

# ✅ Why Use This?

-   Perfect when applications read **configuration from files**
-   No need to rebuild container images
-   Just **update the ConfigMap**
-   Kubernetes automatically updates mounted configuration

------------------------------------------------------------------------

# 📦 ConfigMap `binaryData` (Base64 Encoded)

`data` → Plain text values\
`binaryData` → Base64 encoded binary content

``` yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-binary-configmap
binaryData:
  USER: YWRtaW4=
  PORT: ODA4MA==
  db.conf: aG9zdD1sb2NhbGhvc3QKcG9ydD0zMzA2
```

Decoded values:

    USER = admin
    PORT = 8080
    db.conf =
    host=localhost
    port=3306

------------------------------------------------------------------------

# 🔧 Encode Values (Base64)

    echo -n "admin" | base64
    echo -n "8080" | base64
    echo -n "host=localhost\nport=3306" | base64

For multi‑line files:

    echo -e "host=localhost\nport=3306" > db.conf
    base64 db.conf

------------------------------------------------------------------------

# 🔓 Decode Base64

    echo 'YWRtaW4=' | base64 -d

------------------------------------------------------------------------

# 🗄 Where ConfigMap Data Is Stored?

ConfigMap data is stored inside:

**etcd --- Kubernetes' internal distributed key‑value store.**

------------------------------------------------------------------------

# 📊 Why Application Logs Matter

Application logs help you:

-   🔎 Debug issues
-   📈 Monitor application behavior
-   🧠 Understand what is happening inside containers

Use:

    kubectl logs <pod-name>

------------------------------------------------------------------------

# 🚀 Quick Summary

  Method            Usage
  ----------------- -----------------------------------
  envFrom           All keys as environment variables
  configMapKeyRef   Single key as env variable
  volumeMounts      Mount config as files

------------------------------------------------------------------------

💡 **Best Practice:**\
Use **ConfigMaps for configuration** and **Secrets for sensitive data**.
