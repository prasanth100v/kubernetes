# 🔐 Sealed Secrets Complete Guide (GitOps + Argo CD) 
## ✨ What is a SealedSecret?

 * A **SealedSecret** is a secure way to store **encrypted Kubernetes Secrets in Git**.
 * 👉 Even if someone accesses your Git repository:
      * ❌ They **cannot decrypt** the secret
      * 🔓 Only your **Kubernetes cluster** can decrypt it
      * Inside the cluster, a **Sealed Secrets Controller** decrypts it and converts it into a normal Kubernetes `Secret`.

## 🔐💡 Why is this Useful?

 * 🔒 Safely store secrets in Git
 * 🔑 Only cluster with `private key` can decrypt
 * 🔄 Works perfectly with GitOps tools:
       * Argo CD
       * Flux CD

---

## 🎯🚀 Goal (End-to-End Flow)
```
Secret → Encrypt (kubeseal) → Store in Git → Deploy → Auto Decrypt in Cluster
```

## ⚙️🛠️ Step 1: Install Sealed Secrets Controller
```bash
kubectl apply -f https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.25.0/controller.yaml
```
📌 This installs the controller in the `kube-system` namespace.

### 📦 Install kubeseal CLI
```bash
wget https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.25.0/kubeseal-linux-amd64 -O kubeseal
chmod +x kubeseal
sudo mv kubeseal /usr/local/bin/
```

## 🧾📄 Step 2: Create a Normal Secret (YAML)
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: YWRtaW4=
  password: c2VjcmV0MTIz
```

📌 This creates a file: `my-secret.yaml`

## 🔐🔄 Step 3: Encrypt Secret using kubeseal
```bash
kubeseal --format=yaml < my-secret.yaml > my-sealed-secret.yaml
```
✅ Output file: `my-sealed-secret.yaml`

## 🔒📦 Sample SealedSecret (Safe for Git)
```yaml
apiVersion: bitnami.com/v1alpha1
kind: SealedSecret
metadata:
  name: my-secret
  namespace: default
spec:
  encryptedData:
    username: AgB3Y2F...
    password: AgBjbXc...
```

💡✨ This file is **safe to commit to Git** 🚀

## 🚀📤 Step 4: Apply SealedSecret in Cluster
```bash
kubectl apply -f my-sealed-secret.yaml
```
 * ✅ Controller will:
        * 🔓 Decrypt the secret
        * 📦 Create a normal Kubernetes Secret automatically

## 🔄🚀 Step 5: GitOps Flow with Argo CD
```bash
git add my-sealed-secret.yaml
git commit -m "Add sealed secret for app password"
git push origin main
```

### 🔹⚡ What Happens Next?

  * Argo CD detects changes
  * Syncs manifests to cluster
  * Sealed Secrets Controller:
       * 🔓 Decrypts secret
       * 📦 Creates Kubernetes Secret

## 📦🔗 Using Secret in Deployment
  You can reference the Secret in your Pod/Deployment:

```yaml
env:
- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
      name: my-secret
      key: password
```

## 🔐⚙️ Advanced SealedSecret Example
```yaml
apiVersion: bitnami.com/v1alpha1
kind: SealedSecret
metadata:
  name: my-secret
  namespace: default
spec:
  encryptedData:
    password: AgBy1Fh1...
  template:
    metadata:
      name: my-secret
      namespace: default
```

## 💡 Best Practices

 * ❌ Never store plaintext secrets in Git
 * 🔐 Always use `SealedSecrets` or external tools
 * 🔑 Backup Sealed Secrets `private key` securely
 * 📂 Use namespaces carefully while sealing
 * 🔄 Rotate secrets periodically

---

## 🎯🚀 Final Thought

 * > "Git is public, cluster is private 🔒"
 * Design your system assuming Git can be accessed by anyone, but only your cluster can decrypt secrets.

---

✨🎉 **Happy Secure GitOps with Sealed Secrets! 🚀🔐**
