# 📦 EFK Stack in Kubernetes 
## 🔍 What is EFK?
EFK = Elasticsearch + Fluentd + Kibana 👉 It is a centralized logging solution for Kubernetes.

EFK stands for:
- **Fluentd** → Collects logs 📥  
- **Elasticsearch** → Stores 📦  
- **Kibana** → Visualizes logs 📊  

## 🚀 Why Use EFK in Kubernetes?
💡 Problem: Pod logs are **ephemeral ❌** (lost after restarts/crashes)

✅ Solution: EFK stores logs centrally & permanently ✅


## 📊 Benefits of EFK Stack (Kubernetes Logging)
| 🧩 Feature             | 💡 Description                             |
| ---------------------- | ------------------------------------------ |
| 📦 Centralized Logging | 🗂️ Collect all logs in one place          |
| 🔍 Fast Search         | ⚡ Elasticsearch indexing for quick queries |
| 📊 Visualization       | 🎨 Kibana dashboards for visualization       |
| 🔄 Real-time Logs      | ⏱️ Live log streaming & monitoring         |
| 🔐 Security Monitoring | 🛡️ Detect anomalies & suspicious activity |
| 🧹 Log retention       |    30/90 days                               |


## ⚙️ EFK Architecture
```
Pods/Containers
      ↓
Fluentd (DaemonSet)
      ↓
Elasticsearch (StatefulSet)
      ↓
Kibana (UI Dashboard)
```

## 🔄 How EFK Works
🔁 Flow:

1️⃣ Pods generate logs
2️⃣ Fluentd collects logs from:
   ```
   /var/log/containers/*.log
   ```
3️⃣ Adds metadata (pod, namespace, labels)
4️⃣ Sends logs → Elasticsearch
5️⃣ Kibana → Search & visualize

## 📊 EFK Stack (Logging in Kubernetes)  
| 🧩 Component     | 📌 Role             | 💡 Description                                                                                           |
| ---------------- | ------------------- | -------------------------------------------------------------------------------------------------------- |
| 📥 Fluentd       | 📦 Log Collector    | 🖥️ Runs as DaemonSet, collects logs from `/var/log/containers/*.log` and adds metadata (pod, namespace) |
| 📦 Elasticsearch | 💾 Log Storage      | 📄 Stores logs as JSON documents and enables fast search                                                 |
| 📊 Kibana        | 🎨 Visualization UI | 🔍 Search, analyze logs, and create dashboards                                                           |


## 🚀 EFK Real-Time Use Cases 
| 🧩 Use Case            | 📌 What You Do                                      | 💡 Why it Matters                           |
| ---------------------- | --------------------------------------------------- | ------------------------------------------- |
| 🔍 Debugging Failures  | 🔎 Search logs in Kibana<br>⏱️ Filter by pod & time | 🛠️ Quickly find root cause when pods crash |
| 📜 Audit Logs          | 👤 Track user actions<br>📊 Monitor compliance      | 🛡️ Useful for auditing & governance        |
| 🌐 API Monitoring      | 📈 Request count<br>❌ Error rates                   | 🔍 Understand API performance & issues      |
| 🧹 Log Retention       | 🗂️ Store logs (30/90/180 days)                     | 📚 Helps in history tracking & compliance   |
| 🔐 Security Monitoring | 🚨 Detect failed logins<br>⚠️ Suspicious activity   | 🛡️ Improves system security                |
| 📡 Live Log Tailing    | ⏱️ Real-time logs in Kibana                         | ⚡ Monitor live system behavior              |

---

## 🎯 Interview Answer

👉 EFK is used for **centralized logging in Kubernetes**.

- Fluentd collects logs  
- Elasticsearch stores logs  
- Kibana visualizes logs  

💡 Helps debug issues quickly and monitor microservices.

---

## ⚖️ EFK vs ELK

| 🧩 Feature        | 📦 EFK (Elasticsearch + Fluentd + Kibana) | 📊 ELK (Elasticsearch + Logstash + Kibana) |
| ----------------- | ----------------------------------------- | ------------------------------------------ |
| 📥 Log Collector  | 📄 Fluentd                                | 📊 Logstash                                |
| ⚡ Performance     | 🚀 Lightweight                            | 🐢 Heavier                                 |
| 💾 Resource Usage | ✅ Low (CPU & Memory efficient)            | ❌ High                                     |
| ☸️ Kubernetes Fit | ☸️ Kubernetes-native (DaemonSet friendly)  | ❌ Not optimized for Kubernetes             |
| ⚙️ Deployment     | 👍 Simple  (🔌 Easy Integration)          | ⚠️ More complex                            |
| 📈 Scalability    | 🔥 High in Kubernetes                     | 👍 Good but heavier                        |
| 🔌 Plugins        | 👍 Good ecosystem                         | 🌟 Very rich ecosystem                     |
| 🎯 Use Case       | ☸️ Kubernetes logging                     | 🏢 Enterprise log pipelines                |

## 🔧 Fluentd in Kubernetes (EFK)
✅ How it Works
| 🧩 Step               | 📌 What Happens          | 💡 Explanation                                |
| --------------------- | ------------------------ | --------------------------------------------- |
| 🚀 Runs as DaemonSet  | 🖥️ One pod per node     | 📦 Ensures logs are collected from every node |
| 📂 Log Collection     | 📄 `/var/log/containers` | 📝 Reads container logs from node filesystem  |
| 🏷️ Metadata Addition | ☸️ Pod, namespace info   | 🔍 Adds Kubernetes context to logs            |
| 📤 Send to Storage    | 📦 Elasticsearch         | 🚀 Stores logs for search & analysis          |


## 🔍 Searching Logs in Kibana
Example query:
```
kubernetes.pod_name: "myapp*" AND log_level: "error"
```

## 📦 What is an Index?
- Like a table in DB  
- Stores log documents  
- Enables fast search  

## ⚠️ What if Fluentd Fails?
- 👉 Problem: Logs not collected, Logs from that node are lost  
- ✅ Solutions: Buffering enabled, Health checks (liveness/readiness) & Alerts for failures

---

## 🚀 Deployment Steps

1️⃣ Create namespace:
```bash
kubectl create namespace logging
```
2️⃣ Install Elasticsearch & Kibana (Helm)
👉 Add repo first:
```
helm repo add elastic https://helm.elastic.co
helm repo update
```
👉 Install:
```
helm install elasticsearch elastic/elasticsearch -n logging
helm install kibana elastic/kibana -n logging
```
3️⃣ Deploy Fluentd (DaemonSet) 🔥
👉 Fluentd runs on every node and collects logs

📄 Fluentd DaemonSet (Minimal Example)
```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
  namespace: logging
spec:
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      containers:
      - name: fluentd
        image: fluent/fluentd:v1.16
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 100Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
```

4️⃣ Access Kibana:
```bash
kubectl port-forward svc/kibana 5601:5601 -n logging
```
👉 Open: http://localhost:5601

---

## 🔌 Fluentd Plugins
| 🧩 Plugin                       | 🎯 Purpose        | 💡 What it Does                                           |
| ------------------------------- | ----------------- | --------------------------------------------------------- |
| 📄 `in_tail`                    | 📖 Read log files | 📂 Reads logs from files like `/var/log/containers/*.log` |
| ☸️ `kubernetes_metadata_filter` | 🏷️ Add pod info  | 📌 Adds metadata (pod name, namespace, labels)            |
| 🔄 `record_transformer`         | ✏️ Modify logs    | 🛠️ Changes or enriches log data (add/remove fields)      |
| 📤 `out_elasticsearch`          | 🚀 Send logs      | 📦 Sends logs to Elasticsearch for storage & search       |


## 🏗️ Production Best Practices
| 🧩 Category         | 📌 Best Practice                                                   | 💡 Why it Matters                              |
| ------------------- | ------------------------------------------------------------------ | ---------------------------------------------- |
| 🔒 Security         | 🌐 Use HTTPS (Ingress + TLS)<br>🔑 Authenticate Kibana             | 🛡️ Protect logs & prevent unauthorized access |
| 🧹 Log Optimization | ⚙️ Filter logs (error/warn only)<br>🚫 Avoid unnecessary logs      | 📉 Reduce storage & improve performance        |
| 📊 Retention        | 🗂️ Use ILM (Index Lifecycle Policies)<br>🗑️ Auto-delete old logs | 💾 Save storage & meet compliance needs        |
| ⚡ Scalability       | 📦 Use multiple Elasticsearch nodes<br>💽 Increase storage         | 🚀 Handle high log volume efficiently          |

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
