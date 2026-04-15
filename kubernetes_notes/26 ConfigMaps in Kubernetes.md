# 🎛️🌈 ConfigMaps in Kubernetes 
## 📘 🔍 What is a ConfigMap?

 * A **ConfigMap** is a Kubernetes object used to store **non‑sensitive configuration data** separately from your application code.
 * It stores **key‑value pairs** such as:
     * 🌿 Environment variables
     * ⚙️ Application settings
     * 📁 Configuration files  
 * Pods can consume ConfigMaps in three ways:
     * 🌿 Environment Variables
     * 🖥️ Command-line arguments
     * 📁 Mounted configuration files  

> ⚠️ Maximum size of a ConfigMap is **1MB**.


## 🔧 🚀 Why Use ConfigMaps?

 * ✅ Separate configuration from application code
 * ✅ Update configuration **without rebuilding container images**
 * ✅ Make applications more **portable and flexible**

🚫 **Do NOT store sensitive data here.** Use `Kubernetes Secrets` or `Vault` instead.

## 🧪 📦 Example Use Cases
Typical values stored in ConfigMaps:
 - 📊 `LOG_LEVEL=info`
 - ⚙️ `APP_MODE=prod`
 - 🌐 External API URLs
 - 🗄 Database connection URLs *(non-secret)*

## 🧠 💡 Pro Tips

 * ⚡ ConfigMaps are **not encrypted**.
 * ⚡ Updating a ConfigMap **does not automatically update running pods**.
 * Pods will see changes only if:
      * 🔁 The pod is **restarted**, or
      * 📂 The ConfigMap is **mounted as a volume** and the application reloads the file.
 * Example:
      * Instead of hardcoding a `database URL` inside code, store it in a **ConfigMap**.


# 📦📁 Example ConfigMap

```yaml
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

### 🔍✨ What this ConfigMap does

 * 🔹 `USER` and `PORT` → injected as **environment variables**
 * 🔹 `db.conf` → mounted as a **file inside the container**
 * Example location inside container: `/etc/config/db.conf`

---

# 🔄🔁 Do ConfigMaps Update Live?
| 🧩 **Usage Type**            | 🔄 **Live Update** | 📖 **Behavior**                                                | 💡 **Real-World Explanation**                          |
| ---------------------------- | ------------------ | -------------------------------------------------------------- | ------------------------------------------------------ |
| 🌿 **Environment Variables** | ❌ No               | Values are injected only at Pod startup                        | 👉 Requires **Pod restart** to pick updated values     |
| 📁 **Mounted Volumes**       | ✅ Yes              | ConfigMap updates are reflected automatically in mounted files | 👉 Changes appear inside container (with slight delay) |


 * If used as **env variables**, pods must restart.
 * If used as **volumes**, files update automatically but Changes appear inside container is slight delay..

---

# 🔐🆚 ConfigMap vs Secret
| 🧩 Feature        | 📦 ConfigMap                    | 🔐 Secret                                    |
| ----------------- | ------------------------------- | -------------------------------------------- |
| 📖 Data Type      | 📝 Non-sensitive data           | 🔑 Sensitive data                            |
| 💡 Examples       | ⚙️ Configs, URLs, feature flags | 🔒 Passwords, API keys, tokens               |
| 🔒 Encoding       | 📄 Plain text                   | ⚠️ Base64 encoded (not true encryption)      |
| 🛡 Security Level | ❌ Not secure                    | ⚠️ More secure (supports encryption options) |
| ⚙️ Usage          | 🌿 Env vars / mounted files     | 🔐 Same usage but for secrets                |
| 🔑 Access Control | 🔓 Basic                        | 🛡️ RBAC-controlled access                   |
| 📁 Storage        | 💾 Stored in etcd               | 💾 Stored in etcd (can be encrypted at rest) |

---

# 🚀⚙️ Updating ConfigMap Without Downtime

 * Use **Rolling Updates with Deployments**.
 * Steps:
    * 1️⃣ Update ConfigMap
    * 2️⃣ Trigger deployment rollout
    * 3️⃣ Kubernetes replaces pods gradually

---

# 🧩🔗 Using ConfigMap in a Deployment

```yaml
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

⭐🌟 **ConfigMaps make Kubernetes applications configurable, portable, and easier to manage.**

# 🌈✨ Kubernetes ConfigMap -- Usage Recap
## 🔍🎯 3 Types of ConfigMap Usage

### 1️⃣🌿 All Keys as Environment Variables (`envFrom`)
 Inject all keys from a ConfigMap as environment variables.

```yaml
envFrom:
- configMapRef:
    name: my-configmap
```

Example inside container:

```yaml
$USER=admin
$PORT=8080
```

### 2️⃣🎯 Single Key as Environment Variable (`env + configMapKeyRef`)
 Use a specific key from a ConfigMap as an environment variable.

```yaml
env:
- name: MY_LOG_LEVEL
  valueFrom:
    configMapKeyRef:
      name: my-configmap
      key: log_level
```

Example:
```yaml
$MY_LOG_LEVEL=info
```

### 3️⃣📁 Mount ConfigMap as Files (Volumes)

```yaml
volumeMounts:
- name: config-volume
  mountPath: /etc/config

volumes:
- name: config-volume
  configMap:
    name: my-configmap
```

### 📂✨ Inside the Container

When the container starts:
```yaml
/etc/config/USER      → admin
/etc/config/PORT      → 8080
/etc/config/db.conf   →
host=localhost
port=3306
```

Each **ConfigMap key becomes a file**, and the **value becomes the file content**.

## ✅🎯 Why Use This?

  * 🔹 Perfect when applications read **configuration from files**
  * 🔹 No need to rebuild container images
  * 🔹 Just **update the ConfigMap**
  * 🔹 Kubernetes automatically updates mounted configuration

## 📦🔐 ConfigMap `binaryData` (Base64 Encoded)

 `data` → Plain text values
 `binaryData` → Base64 encoded binary content

```yaml
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

```yaml
USER = admin
PORT = 8080
db.conf =
host=localhost
port=3306
```

# 🔧🧾 Encode Values (Base64)

```bash
echo -n "admin" | base64
echo -n "8080" | base64
echo -n "host=localhost\nport=3306" | base64
```

For multi‑line files:
```bash
echo -e "host=localhost\nport=3306" > db.conf
base64 db.conf
```

# 🔓🔍 Decode Base64
```bash
echo 'YWRtaW4=' | base64 -d
```

## 🗄📦 Where ConfigMap Data Is Stored?

 ConfigMap data is stored inside: **etcd --- Kubernetes' internal distributed key‑value store.**

## 📊📈 Why Application Logs Matter

 * Application logs help you:
    * 🔎 Debug issues
    * 📈 Monitor application behavior
    * 🧠 Understand what is happening inside containers

Use:
```bash
kubectl logs <pod-name>
```

---

# 🚀⚡ Quick Summary

| 🧩 **Method**          | 📖 **Usage**                                 | 🧠 **How It Works**                                            | 💡 **Example Use Case**                                     |
| ---------------------- | -------------------------------------------- | -------------------------------------------------------------- | ----------------------------------------------------------- |
| 🌿 **envFrom**         | Load all keys as environment variables       | 👉 Imports every key from ConfigMap into container as env vars | When you want **all configs** (DB_HOST, PORT, etc.) quickly |
| 🔑 **configMapKeyRef** | Load a single key as an environment variable | 👉 Selects a specific key from ConfigMap                       | When you need **only one value** (e.g., DB_PASSWORD)        |
| 📁 **volumeMounts**    | Mount ConfigMap as files inside container    | 👉 Each key becomes a file in a directory                      | When app reads config from **files (e.g., config.yaml)**    |

---

# 📌🎉 Summary

✔ ConfigMaps store **non-sensitive configuration**
✔ Separate **config from container images**
✔ Can inject into pods as **env variables or files**
✔ **Secrets should be used for sensitive data**

---

💡✨ **Best Practice:**
       * Use **ConfigMaps for configuration** and **Secrets for sensitive data**.
