# 🔐 Kubernetes Secrets -- Quick Guide

## 📌 What are Kubernetes Secrets?

Kubernetes **Secrets** are used to store **sensitive data** such as:

-   🔑 Passwords
-   🔐 Tokens
-   🔑 SSH Keys
-   🌐 API Keys
-   📜 TLS Certificates

Secrets are similar to **ConfigMaps**, but designed for **sensitive
information**.

⚠️ **Important Notes** - Secrets are **base64 encoded**, not encrypted
by default. - Individual Secret size limit: **1 MB** - Use **RBAC** to
restrict access. - Avoid exposing secrets in Git manifests.

------------------------------------------------------------------------

# 🚀 Creating Secrets (Imperative CLI)

## 1️⃣ Create Secret from literal values

``` bash
kubectl create secret generic my-secret   --from-literal=username=admin   --from-literal=password=secret123
```

## 2️⃣ Create Secret from a file

``` bash
kubectl create secret generic tls-cert   --from-file=cert.crt   --from-file=cert.key
```

## 3️⃣ Create Secret from a directory

(All files inside directory become keys)

``` bash
kubectl create secret generic config-secret   --from-file=/path/to/dir/
```

## 4️⃣ Create TLS Secret

Used for **Ingress / HTTPS communication**

``` bash
kubectl create secret tls my-tls-secret   --cert=cert.pem   --key=key.pem
```

## 5️⃣ Docker Registry Secret

Used to pull **private container images**

``` bash
kubectl create secret docker-registry regcred --docker-username=your-user --docker-password=your-pass --docker-email=you@example.com
```

## 6️⃣ SSH Secret

``` bash
kubectl create secret generic ssh-key-secret --type=kubernetes.io/ssh-auth --from-file=ssh-privatekey=id_rsa
```

------------------------------------------------------------------------

# 🔍 Viewing Secrets

``` bash
kubectl get secrets
kubectl describe secret my-secret
kubectl get secret my-secret -o yaml
```

### Encode / Decode Base64

Encode

``` bash
echo -n 'admin' | base64
```

Decode

``` bash
echo 'YWRtaW4=' | base64 -d
```

------------------------------------------------------------------------

# 📦 Types of Kubernetes Secrets

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
  password: cGFzczEyMw==
```

------------------------------------------------------------------------

## 2️⃣ Docker Registry Secret

``` yaml
apiVersion: v1
kind: Secret
metadata:
  name: regcred
type: kubernetes.io/dockerconfigjson
data:
  .dockerconfigjson: <base64-encoded-json>
```

------------------------------------------------------------------------

## 3️⃣ Basic Auth Secret

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

------------------------------------------------------------------------

## 4️⃣ SSH Secret

``` yaml
apiVersion: v1
kind: Secret
metadata:
  name: ssh-key-secret
type: kubernetes.io/ssh-auth
data:
  ssh-privatekey: <base64-encoded-private-key>
```

------------------------------------------------------------------------

## 5️⃣ TLS Secret

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

------------------------------------------------------------------------

# 📦 Using Secrets in Pods

## Environment Variables

``` yaml
env:
- name: DB_USER
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: username
```

------------------------------------------------------------------------

## Mount Secrets as Files

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

------------------------------------------------------------------------

## Image Pull Secret

``` yaml
spec:
  containers:
  - name: private-container
    image: myregistry.io/private-image
  imagePullSecrets:
  - name: regcred
```

------------------------------------------------------------------------

## TLS Secret with Ingress

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

------------------------------------------------------------------------

# 🛠️ Useful Secret Commands

``` bash
# List secrets
kubectl get secrets

# Describe secret
kubectl describe secret my-secret

# View secret YAML
kubectl get secret my-secret -o yaml

# Edit secret
kubectl edit secret my-secret

# Delete secret
kubectl delete secret my-secret

# Apply secret from YAML
kubectl apply -f my-secret.yaml

# List secrets in namespace
kubectl get secrets -n <namespace>
```

------------------------------------------------------------------------

# 📚 ConfigMap Commands (Reference)

``` bash
kubectl get configmaps
kubectl describe configmap my-config
kubectl get configmap my-config -o yaml
```

------------------------------------------------------------------------

✅ **Tip:** Use **Secrets + RBAC + Encryption at Rest** for better
security in production clusters.
