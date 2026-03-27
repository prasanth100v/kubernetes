# 📦 EFK Stack in Kubernetes 
## 🔍 What is EFK?

EFK = Elasticsearch + Fluentd + Kibana 👉 It is a centralized logging solution for Kubernetes.

EFK stands for:
- **Fluentd** → Collects logs 📥  
- **Elasticsearch** → Stores & indexes logs 📦  
- **Kibana** → Visualizes logs 📊  

---

## 🚀 Why Use EFK in Kubernetes?

💡 Problem:
- Pod logs are **ephemeral** (lost after restart)

✅ Solution:
- Centralized logging system

### Benefits:
- 🔍 Search logs across all pods  
- 🧪 Debug microservices  
- 🔐 Security monitoring  
- 🧹 Log retention (30/90 days)  
- 📊 Dashboard visualization  

---

## ⚙️ EFK Architecture

```
[ Pods / Containers ]
        ↓
[ Fluentd (DaemonSet) ]
        ↓
[ Elasticsearch ]
        ↓
[ Kibana ]
```

---

## 🔧 Components Explained

### 📥 Fluentd
- Runs as **DaemonSet**
- Collects logs from:
```
/var/log/containers/*.log
```
- Adds metadata (pod, namespace)

---

### 📦 Elasticsearch
- Stores logs as JSON documents  
- Enables fast search  

---

### 📊 Kibana
- UI to search & visualize logs  
- Create dashboards  

---

## 🚀 Real-Time Use Cases

| Use Case | EFK Role |
|----------|---------|
| Debug app failures | Search logs by pod |
| Audit logs | Track user/system actions |
| API monitoring | Analyze request logs |
| Security | Detect anomalies |
| Live tailing | View real-time logs |

---

## 🎯 Interview Answer

👉 EFK is used for **centralized logging in Kubernetes**.

- Fluentd collects logs  
- Elasticsearch stores logs  
- Kibana visualizes logs  

💡 Helps debug issues quickly and monitor microservices.

---

## ⚖️ EFK vs ELK

- EFK uses **Fluentd**
- ELK uses **Logstash**

👉 Fluentd is:
- Lightweight  
- Kubernetes-native  

---

## 🔍 Searching Logs in Kibana

Example query:

```
kubernetes.pod_name: "myapp*" AND log_level: "error"
```

---

## 📦 What is an Index?

- Like a table in DB  
- Stores log documents  
- Enables fast search  

---

## ⚠️ What if Fluentd Fails?

- Logs from that node are lost  
- Solution:
  - Buffering  
  - Health checks  
  - Alerts  

---

## 🚀 Deployment Steps

1️⃣ Create namespace:
```bash
kubectl create namespace logging
```

2️⃣ Install Elasticsearch & Kibana (Helm)

3️⃣ Deploy Fluentd as DaemonSet

4️⃣ Access Kibana:
```bash
kubectl port-forward svc/kibana 5601:5601
```

👉 http://localhost:5601  

---

## 🔌 Fluentd Plugins

- in_tail → Read logs  
- kubernetes_metadata_filter → Add metadata  
- record_transformer → Modify logs  
- out_elasticsearch → Send logs  

---

## 🏗️ Production Best Practices

- 🔐 Secure Kibana with TLS  
- 📦 Use Persistent Volumes  
- 🧹 Configure log retention  
- 🎯 Filter logs (error/warn only)  
- 📊 Create dashboards  

---

## 🧠 Summary

EFK provides:
- 📊 Centralized logging  
- 🔍 Fast search  
- 🚀 Real-time debugging  
- ☸️ Kubernetes-native logging  

👉 Essential for production monitoring & troubleshooting 💥
