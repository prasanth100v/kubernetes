# 🔐 DigiCert TLS in Kubernetes 
## 📌 What is DigiCert?
DigiCert is a **globally trusted Certificate Authority (CA)** that provides SSL/TLS certificates. 👉 It provides publicly trusted SSL/TLS certificates 

💡 Used for:
  - 🌐 Websites (HTTPS)
  - 🔗 APIs
  - ☸️ Kubernetes Ingress & Gateway API
  - 🕸️ Service Mesh (Istio)
  - 📱 Apps & IoT


## 🚀 Why Use DigiCert?
👉 When you need trusted, production-grade security
| 🧩 Feature             | 💡 Why it Matters                                      |
| ---------------------- | ------------------------------------------------------ |
| 🔐 Global Trust        | 🌍 Trusted by all browsers & OS → no security warnings |
| 🏢 Enterprise Security | 🛡️ Strong validation ensures safe & compliant systems |
| ⚡ Fast Issuance        | ⏱️ Certificates can be issued quickly (DV in minutes)  |
| 📜 Multiple Options    | 🎯 Choose from DV, OV, EV, Wildcard, SAN certificates  |
| 🔄 Reliability         | 💪 Stable, trusted, and widely supported               |
| Long Validity           | ⚡ Up to 1 year                                           |

## 🔑 Types of DigiCert Certificates

| 🧩 Type                     | 📌 Description                  | 🎯 Use Case                                                        |
| ------------------------------- | ------------------------------- | ------------------------------------------------------------------ |
| ✅ DV (Domain Validation)      | 🌐 Verifies domain ownership only⚡ (Fast & cheap)       | 📝 Blogs, personal & small sites         |
| 🏢 OV (Organization Validation)| 🏢 Verifies domain + company                              | 💼 Business websites & apps              |
| 🏦 EV (Extended Validation)   | 🏛️ 🔥 Highest trust level + Shows company name in browser | 💳 Banking, e-commerce, finance          |
| 🌟 Wildcard Certificate       | 🌐 Covers all subdomains                                   | 🔗 `*.example.com` ( api.example.com, multiple subdomains) |
| 🌍 Multi-domain (SAN)         | 🌐 Multiple domains in one cert                            | 🧩 Multi-app / (app.org, shop.net) multi-domain systems      |

### 🔐 How DigiCert Works (Trust Chain)
🔗 Trust Chain:

- 1️⃣ Root CA (trusted globally)
- 2️⃣ Intermediate CA
- 3️⃣ Your certificate

   ✔️ Browser verifies full chain → shows 🔒 padlock

## 🔧 How to Use DigiCert TLS in Kubernetes
👉 Instead of self-signed or Let’s Encrypt, you use DigiCert certs
 - ✔️ Best for :  Enterprise apps, Public production systems

### 1️⃣🔐 Generate CSR (Certificate Signing Request)

```bash
openssl req -new -newkey rsa:2048 -nodes \
-keyout myapp.key \
-out myapp.csr \
-subj "/C=IN/ST=YourState/L=YourCity/O=YourOrg/CN=myapp.example.com"
```

🔹 Output:
- myapp.key → Private key 🔑
- myapp.csr → Upload to DigiCert 📄

## 📌 CSR Details
Replace values:
- C → Country (IN)
- ST → State
- L → City
- O → Organization
- CN → Domain name (Prasanthpoultry.com)

## 🛒 Buy Certificate from DigiCert
1️⃣ Go to DigiCert website  
2️⃣ Choose certificate type  
3️⃣ Upload CSR  
4️⃣ Complete verification

👉 You will receive:
- certificate.crt  
- intermediate.crt  

## 🔗 Create Full Chain
```bash
cat certificate.crt intermediate.crt > fullchain.crt
```

## 🔐 Create Kubernetes TLS Secret
```bash
kubectl create secret tls myapp-tls \
--cert=fullchain.crt \
--key=myapp.key \
--namespace=default
```

## 🌐 Use in Ingress
```yaml
tls:
- hosts:
  - myapp.example.com
  secretName: myapp-tls
```

## 🌍 Access Your App
👉 https://myapp.example.com

- ✅ Secured with DigiCert
- ✅ Trusted by browsers
- ✅ No security warnings  

---

## 🔐 DigiCert vs Let’s Encrypt vs Self-Signed
| 🧩 Feature    | 🏢 DigiCert               | 🌍 Let’s Encrypt         | 🧪 Self-Signed            |
| ------------- | ------------------------- | ------------------------ | ------------------------- |
| 🤝 Trust      | ✅ High (globally trusted) | ✅ High (browser trusted) | ❌ Not trusted by browsers |
| 💰 Cost       | 💵 Paid                   | 🆓 Free                  | 🆓 Free                   |
| ⚙️ Automation | ⚠️ Partial                | ✅ Full (auto renew)      | ❌ Manual                  |
| 🎯 Use Case   | 🏦 Enterprise apps        | 🌐 Public websites       | 🧪 Testing / internal use |


## 🧠 Summary
### ⚠️ Important Notes
 - 🔑 Keep private key safe
 - 🔄 Renew before expiry
 - 📜 Always include intermediate cert
 - ❌ Don’t expose raw cert files publicly

### DigiCert provides:
- 🔐 High-trust certificates  
- 🏢 Enterprise-grade security  
- ☸️ Perfect for Kubernetes Ingress  
- 🌍 Globally trusted HTTPS  

### 👉 E-commerce app:
  - 🌐 Ingress → DigiCert TLS
  - 🛒 Customers → HTTPS secure
  - 🔐 Payment data protected
  - 🏢 Trusted brand (no warnings)

👉 Best choice for production apps 🚀
