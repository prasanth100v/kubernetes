# 🔐 Kubernetes Secrets Security Guide (EKS + GitOps)

------------------------------------------------------------------------

## ⚠️ Base64 Is NOT Encryption

-   **Base64 = Encoding only**
-   Anyone with access can easily decode it.
-   Real protection requires **encryption at rest**.

------------------------------------------------------------------------

## 🛡️ Protecting Kubernetes Secrets

### 🔑 Encryption at Rest (Amazon EKS)

Amazon **EKS** supports encryption at rest using **AWS KMS**.

-   Secrets stored in **etcd** are encrypted.
-   Encryption keys are managed by **AWS KMS**.
-   Only **authorized IAM roles/users** can decrypt them.

📌 Important: \> Encryption must be enabled **during cluster
creation**.\
It **cannot be added later**.

### Example

``` bash
eksctl create cluster --encrypt-secrets
```

------------------------------------------------------------------------

## 🧠 Self‑Managed Clusters (kubeadm)

Encryption is **NOT enabled by default**.

You must manually configure:

``` yaml
kind: EncryptionConfiguration
```

------------------------------------------------------------------------

# 🔹 Secret Storage Format

Secrets are stored as:

    base64 encoded key-value pairs

Example:

``` yaml
data:
  password: cGFzc3dvcmQ=
```

⚠️ This **avoids plain text** but **is not encrypted**.

------------------------------------------------------------------------

# 📦 Mounting Secrets into Pods

## Option 1 --- Environment Variables

✔ Good for simple secrets.

``` yaml
env:
- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
      name: my-secret
      key: password
```

------------------------------------------------------------------------

## Option 2 --- Volume Mounts

✔ Best for certificates or config files.

``` yaml
volumeMounts:
- name: secret-volume
  mountPath: /app/secret
```

------------------------------------------------------------------------

# 📂 Namespace Scope

Secrets are **namespace-scoped**.

Meaning:

-   A secret must exist **inside the same namespace**
-   Pods in that namespace can reference it

------------------------------------------------------------------------

# 🔒 Restrict Secret Access (RBAC)

Use **Role / ClusterRole** to limit access.

``` yaml
apiGroups: [""]
resources: ["secrets"]
verbs: ["get"]
```

Bind it using:

    RoleBinding

------------------------------------------------------------------------

# 🌐 TLS Certificates for HTTPS Apps

Create a TLS Secret:

``` bash
kubectl create secret tls my-tls-secret --cert=cert.pem --key=key.pem
```

Mount inside Pod:

``` yaml
volumes:
- name: tls-secret
  secret:
    secretName: my-tls-secret

volumeMounts:
- name: tls-secret
  mountPath: "/etc/tls"
  readOnly: true
```

------------------------------------------------------------------------

# 🚫 Prevent Reading Secrets in Cluster

Best practices:

✅ Enable **encryption at rest (KMS)**\
✅ Use **RBAC restrictions**\
✅ Use **external secret managers**

Examples:

-   AWS Secrets Manager
-   HashiCorp Vault

------------------------------------------------------------------------

# 🔄 Updating Secrets Without Restart

### Environment Variables

❌ Requires **Pod restart**

### Mounted Volumes

✔ Updates automatically (\~1 minute)

⚠️ Application must reload the file.

------------------------------------------------------------------------

# 🚀 GitOps Secret Management (ArgoCD)

Never commit **plain Secrets** to Git.

Instead use:

## 🔐 Sealed Secrets (Bitnami)

Safe GitOps workflow.

Process:

1️⃣ Create Secret locally\
2️⃣ Encrypt using **kubeseal**\
3️⃣ Commit **SealedSecret YAML** to Git\
4️⃣ ArgoCD deploys it\
5️⃣ Controller decrypts and creates real Secret

------------------------------------------------------------------------

# 🧰 Popular Secret Management Tools

### HashiCorp Vault

Advanced dynamic secrets and policy control.

### Sealed Secrets

Encrypt secrets for **safe Git storage**.

### External Secrets Operator

Sync secrets from:

-   AWS Secrets Manager
-   HashiCorp Vault
-   Other providers

------------------------------------------------------------------------

# 📁 Mount ConfigMap + Secret Together

``` yaml
volumeMounts:
- name: config-volume
  mountPath: /app/config

- name: secret-volume
  mountPath: /app/secret

volumes:
- name: config-volume
  configMap:
    name: my-config

- name: secret-volume
  secret:
    secretName: my-secret
```

Mounted files:

    /app/config/config-key → config-value
    /app/secret/secret-key → supersecret

------------------------------------------------------------------------

# ✅ Kubernetes Secret Security Checklist

✔ Enable **encryption at rest**\
✔ Restrict access with **RBAC**\
✔ Avoid committing secrets to Git\
✔ Use **Sealed Secrets / Vault**\
✔ Use **volume mounts for certificates**\
✔ Rotate secrets regularly

------------------------------------------------------------------------

🚀 **Kubernetes Secrets Done Right = Secure Clusters + Safe GitOps**
