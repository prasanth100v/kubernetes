## 👉 IRSA = OIDC + IAM role + ServiceAccount
“OIDC is the authentication mechanism, while IRSA is how Amazon EKS uses OIDC to securely grant IAM permissions to Kubernetes pods.”

### 🔹IAM User
  A long-term AWS identity with access keys (username/password or keys).

### 🔹OIDC (OpenID Connect)
   A keyless authentication mechanism using identity tokens to assume IAM roles.

### 🔹IRSA (IAM Roles for Service Accounts)
   An Amazon EKS feature that uses OIDC to give IAM roles to Kubernetes pods.


## ✅ 1. Use IRSA (IAM Roles for Service Accounts) Why?
```
Avoids storing AWS access keys in pods ❌
Secure, fine-grained IAM access ✅
Mandatory for production EKS setups
```
