# 🔐✨ Kubernetes Secrets -- Quick Guide ✨🔐
## 📌🌟 What are Kubernetes Secrets?

 * Kubernetes **Secrets** are used to store **sensitive data** such as:
     * 🔑 Passwords
     * 🔐 Tokens
     * 🔑 SSH Keys
     * 🌐 API Keys
     * 📜 TLS Certificates

⚠️ 🚨 **Important Notes**
   - Secrets are **base64 encoded**, not encrypted by default
   - Individual Secret size limit: **1 MB**
   - Use **RBAC** to restrict access
   - Avoid exposing secrets in Git manifests
   - Secrets are similar to **ConfigMaps**, but designed for **sensitive information**.


# 🚀✨ Creating Secrets (Imperative CLI)
## 1️⃣📦 Create Secret from literal values
``` bash
kubectl create secret generic my-secret   --from-literal=username=admin   --from-literal=password=secret123
```

## 2️⃣📁 Create Secret from a file
``` bash
kubectl create secret generic tls-cert   --from-file=cert.crt   --from-file=cert.key
```

## 3️⃣📂 Create Secret from a directory

(All files inside directory become keys)
``` bash
kubectl create secret generic config-secret   --from-file=/path/to/dir/
```

## 4️⃣🔐 Create TLS Secret

Used for **Ingress / HTTPS communication**
``` bash
kubectl create secret tls my-tls-secret   --cert=cert.pem   --key=key.pem
```

## 5️⃣🐳 Docker Registry Secret

Used to pull **private container images**
``` bash
kubectl create secret docker-registry regcred --docker-username=your-user --docker-password=your-pass --docker-email=you@example.com
```

## 6️⃣🔑 SSH Secret

``` bash
kubectl create secret generic ssh-key-secret --type=kubernetes.io/ssh-auth --from-file=ssh-privatekey=id_rsa
```

# 🔍👀 Viewing Secrets

``` bash
kubectl get secrets
kubectl describe secret my-secret
kubectl get secret my-secret -o yaml
```

### 🔐 Encode / Decode Base64

Encode
``` bash
echo -n 'admin' | base64
```

Decode
``` bash
echo 'YWRtaW4=' | base64 -d
```

# 📦📚 Types of Kubernetes Secrets
## 1️⃣ Opaque Secret (Default)

Used for generic key-value data.
``` yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
  namespace: my-namespace
type: Opaque
data:
  username: YWRtaW4=
  password: cGFzczEyMw==   #Not encrypted, just encoded.
```

## 2️⃣🐳 Docker Registry Secret

``` yaml
apiVersion: v1
kind: Secret
metadata:
  name: regcred
type: kubernetes.io/dockerconfigjson
data:
  .dockerconfigjson: <base64-encoded-json>
```

## 3️⃣🔐 Basic Auth Secret

``` yaml
apiVersion: v1
kind: Secret
metadata:
  name: basic-auth-secret
type: kubernetes.io/basic-auth
stringData:
  username: myuser
  password: mypassword
```

## 4️⃣🔑 SSH Secret

``` yaml
apiVersion: v1
kind: Secret
metadata:
  name: ssh-key-secret
type: kubernetes.io/ssh-auth
data:
  ssh-privatekey: <base64-encoded-private-key>
```

## 5️⃣📜 TLS Secret

``` yaml
apiVersion: v1
kind: Secret
metadata:
  name: tls-secret
type: kubernetes.io/tls
data:
  tls.crt: <base64-cert>
  tls.key: <base64-key>
```

# 📦⚙️ Using Secrets in Pods
## 🌿 Environment Variables

``` yaml
env:
- name: DB_USER
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: username
```

## 📁 Mount Secrets as Files

``` yaml
volumeMounts:
- name: secret-volume
  mountPath: "/etc/secrets"
  readOnly: true

volumes:
- name: secret-volume
  secret:
    secretName: db-secret
```

## 🐳 Image Pull Secret

``` yaml
spec:
  containers:
  - name: private-container
    image: myregistry.io/private-image
  imagePullSecrets:
  - name: regcred
```

## 🌐 TLS Secret with Ingress

``` yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  tls:
  - hosts:
    - myapp.example.com
    secretName: tls-secret
  rules:
  - host: myapp.example.com
```

## 🛠️🧰 Useful Secret Commands

``` bash
kubectl get secrets                         # List secrets

kubectl describe secret my-secret            # Describe secret

kubectl get secret my-secret -o yaml         # View secret YAML

kubectl edit secret my-secret                # Edit secret

kubectl delete secret my-secret               # Delete secret

kubectl apply -f my-secret.yaml              # Apply secret from YAML

kubectl get secrets -n <namespace>             # List secrets in namespace
```

## 📚📌 ConfigMap Commands (Reference)

``` bash
kubectl get configmaps
kubectl describe configmap my-config
kubectl get configmap my-config -o yaml
```

✅ 💡✨ **Tip:** Use **Secrets + RBAC + Encryption at Rest** for better security in production clusters.

---

## EKS + KMS Encryption Flow
 * KMS protects secrets at rest (inside etcd).
 * In EKS, KMS encrypts Kubernetes Secrets before they are stored in `etcd`.
 * When an application or authorized user requests a Secret, the Kubernetes API server uses `KMS to decrypt it` and return the `plaintext value`.
 * KMS protects Secrets at rest, while Kubernetes `RBAC` controls which users can view those Secrets.
    * If a user has verbs: `- get` `- list` `- watch` on Secrets, they can view them.

### 🔐 How to Enable KMS Encryption in Amazon EKS
| 🔢 **Step** | 🧩 **Action**                | 📖 **Description**                                                                                            |
| ----------- | ---------------------------- | --------------------------------------------------------------------------------------------------------------- |
| 1️⃣         | 🔑 Create AWS KMS Key        | Create a customer-managed KMS key in AWS KMS                                                                   |
| 2️⃣         | ☸️ Associate KMS with EKS    | Enable EKS Secret encryption using the KMS key during cluster creation or with `associate-encryption-config` after EKS creation |
| 3️⃣         | 📝 Create Kubernetes Secrets | Create Secrets normally using `kubectl create secret` or `YAML manifests  `                                    |
| 4️⃣         | 🔒 Encrypt Before Storage    | EKS encrypts Secret data before storing it in etcd                                                             |
| 5️⃣         | 💾 Store in etcd             | Encrypted Secret is stored in the Kubernetes database (`etcd`)                                                 |
| 6️⃣         | 🔓 Decrypt on Access         | API Server uses KMS to decrypt the Secret when an authorized request is made                                   |
| 7️⃣         | 📦 Pod Consumes Secret       | Authorized Pods receive the plaintext Secret as `environment variables` or `mounted files`                     |

```hcl
kubectl create secret
        ↓
Kubernetes API Server
        ↓
AWS KMS Encrypts Secret
        ↓
Encrypted Data Stored in etcd
        ↓
Pod Requests Secret
        ↓
API Server Calls KMS
        ↓
KMS Decrypts Secret
        ↓
Pod Receives Secret
```

---

## 🎯 Kubernetes ConfigMaps & Secrets
| 🔢     | ❓ Question                                               | ✅ Answer                                                                                     |
| ------ | -------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| 🌈 1   | What is a ConfigMap?                                     | 📦 A ConfigMap stores **non-sensitive configuration data** as `key-value pairs`.               |
| 🔐 2   | What is a Secret?                                        | 🔑 A Secret stores **sensitive data** such as passwords, tokens, API keys, and certificates. |
| ⚡ 3    | Why use ConfigMaps?                                      | 🎛️ To separate configuration from application code.                                         |
| 🛡️ 4  | Why use Secrets?                                         | 🔒 To securely manage sensitive information.                                                 |
| 🔄 5   | ConfigMap vs Secret?                                     | 📦 ConfigMap = Non-sensitive data, 🔐 Secret = Sensitive data.                               |
| 💾 6   | How is Secret data stored in etcd?                       | 📄 Base64 encoded by default (not encrypted unless encryption is enabled).                   |
| 🚀 7   | Create a ConfigMap command?                              | `kubectl create configmap app-config --from-literal=env=prod`                                |
| 🔑 8   | Create a Secret command?                                 | `kubectl create secret generic db-secret --from-literal=password=admin123`                   |
| 📂 9   | How can a Pod consume a ConfigMap?                       | 🌿 Environment Variables, 📁 Volume Mounts, 🖥️ Command-line Arguments.                      |
| 📂 10  | How can a Pod consume a Secret?                          | 🌿 Environment Variables or 📁 Volume Mounts.                                                |
| ⚙️ 11  | Which field loads ConfigMap as env vars?                 | `envFrom`                                                                                    |
| 🌿 12  | Which field loads a specific key as env var?             | `env`                                                                                        |
| 📁 13  | Which field mounts ConfigMap as files?                   | `volumes` + `configMap`                                                                      |
| 🔑 14  | Which field mounts Secret as files?                      | `volumes` + `secret`                                                                         |
| 🎯 15  | What is the maximum size of a ConfigMap?                 | 📏 1 MB                                                                                      |
| 🎯 16  | What is the maximum size of a Secret?                    | 📏 1 MB                                                                                      |
| 🔄 17  | Do mounted ConfigMaps update automatically?              | ✅ Yes, eventually (except when using `subPath`).                                             |
| 🔄 18  | Do mounted Secrets update automatically?                 | ✅ Yes, eventually (except when using `subPath`).                                             |
| 🚫 19  | Does a Pod restart automatically when ConfigMap changes? | ❌ No.                                                                                        |
| 🚫 20  | Does a Pod restart automatically when Secret changes?    | ❌ No.                                                                                        |
| 🛠️ 21 | How to view ConfigMaps?                                  | `kubectl get configmaps`                                                                     |
| 🔍 22  | How to view Secrets?                                     | `kubectl get secrets`                                                                        |
| 📖 23  | Describe a ConfigMap?                                    | `kubectl describe configmap <name>`                                                          |
| 📖 24  | Describe a Secret?                                       | `kubectl describe secret <name>`                                                             |
| 🗑️ 25 | Delete a ConfigMap?                                      | `kubectl delete configmap <name>`                                                            |
| 🗑️ 26 | Delete a Secret?                                         | `kubectl delete secret <name>`                                                               |
| 🌍 27  | Are ConfigMaps namespace-scoped?                         | ✅ Yes                                                                                        |
| 🌍 28  | Are Secrets namespace-scoped?                            | ✅ Yes                                                                                        |
| 📄 29  | Can ConfigMaps store files?                              | ✅ Yes                                                                                        |
| 📄 30  | Can Secrets store certificates?                          | ✅ Yes                                                                                        |
| 🔐 31  | Secret type for TLS certificates?                        | `kubernetes.io/tls`                                                                          |
| 🐳 32  | Secret type for Docker registry?                         | `kubernetes.io/dockerconfigjson`                                                             |
| 🛡️ 33 | Best practice for Secrets?                               | 🔒 Enable etcd encryption and RBAC.                                                          |
| 🎯 34  | Can a Pod use multiple ConfigMaps?                       | ✅ Yes                                                                                        |
| 🎯 35  | Can a Pod use multiple Secrets?                          | ✅ Yes                                                                                        |
| 🔄 36  | How to update a ConfigMap?                               | `kubectl edit configmap <name>`                                                              |
| 🔄 37  | How to update a Secret?                                  | `kubectl edit secret <name>`                                                                 |
| 📦 38  | What is `stringData` in Secrets?                         | ✍️ Allows plain text input without Base64 encoding.                                          |
| 🔑 39  | What is `data` in Secrets?                               | 📄 Stores Base64 encoded values.                                                             |
| 🚀 40  | Which is more secure?                                    | 🔐 Secret (with encryption enabled)                                                          |
