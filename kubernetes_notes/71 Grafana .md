# 📊 Grafana
## 🔍 What is Grafana?
Grafana is an **open-source visualization & dashboard tool** used to monitor, analyze, and visualize:

- 📊 Metrics, Logs and Traces
- 🔍 Analyze system performance
- 🚨 Set alerts
- 📈 Monitor applications

### 🎯 Why Grafana?
🔥 Key Benefits:

 - 📊 Beautiful dashboards
 - 🔌 Supports multiple data sources
 - ⚡ Real-time monitoring
 - 🚨 Built-in alerting
 - ☸️ Perfect for Kubernetes

## 🚀 **Prometheus = Metrics Collection** **Grafana = Visualization + Alerting**

👉 Together they provide full observability.

---

## 📦 Install Grafana (Helm)

```bash
helm install grafana grafana/grafana
```

## 🔌 Connect Grafana to Prometheus
🪜 Steps:

1️⃣ Go to **Grafana UI → Settings → Data Sources**  
2️⃣ Select **Prometheus**  
3️⃣ Enter URL:
```
http://prometheus:9090
```
4️⃣ Click **Save & Test** ✅

---

## 🔗 Grafana with Prometheus (Kubernetes Setup)
🔄 Flow:

1️⃣ Prometheus collects metrics  
2️⃣ Grafana connects as data sources  
3️⃣ Dashboards visualize data  
4️⃣ Alerts triggered (Grafana/Prometheus)  


## 📊 What is a Panel?
A **Panel** is a single visualization unit in Grafana.

### Types of Panels:
- 📈 Graph (CPU usage)
- 📊 Bar chart
- 🔢 Stat (single value)
- 📉 Time-series

👉 Each panel runs a query and displays results.


### 📊 What is a Dashboard?
 👉 A Dashboard = Collection of visual panels
- ✔️ Shows: CPU usage, Memory usage & Network traffic and Pod restarts

## 🧠 Common Dashboards

- Node CPU & Memory usage 🖥️  
- Pod resource usage ☸️  
- Application metrics 📡  
- Request latency & errors ⚡  

## 🚨 Alerting in Grafana

Grafana supports alerting:
- 🔔 Example: CPU > 80% & Pod restart > 3
- Threshold-based alerts 🚨  
- Notifications (Email, Slack) 📩  
- Incident monitoring 🔍  


## 📊 Prometheus Real Use Cases (Kubernetes)
| 🧩 Use Case               | 📌 What to Monitor                           | 💡 Why it Matters                            |
| ------------------------- | -------------------------------------------- | -------------------------------------------- |
| 🚀 Cluster Monitoring     | 🖥️ Node CPU, memory<br>💾 Disk usage        | 📊 Track overall cluster health & capacity   |
| 📦 Pod Monitoring         | 🔄 Pod restarts<br>📈 Resource usage         | 🚨 Detect issues like **CrashLoopBackOff**   |
| 🌐 Application Monitoring | 📊 Request rate<br>⏱️ Latency<br>❌ Errors    | 🔍 Understand app performance & failures     |
| 🕸️ Microservices (Istio) | 🔀 Traffic flow<br>🔁 Retries<br>💥 Failures | 🛡️ Observe service-to-service communication |


## 🌟 Features

- Beautiful dashboards 🎨  
- Multiple data sources 🔗  
- Real-time monitoring ⏱️  
- Custom queries (PromQL, etc.) 🧪  


### 📊 Grafana vs Prometheus
| 🧩 Feature      | 📊 Grafana                        | 📈 Prometheus                     |
| --------------- | --------------------------------- | --------------------------------- |
| 🎯 Role         | 🎨 Visualization (dashboards)     | 📊 Metrics collection & storage   |
| 💾 Data Storage | ❌ No (uses external data sources) | ✅ Yes (stores metrics)            |
| 🧪 Query        | 🔎 Uses PromQL (via Prometheus)   | 🧠 Provides PromQL                |
| 🔔 Alerting     | 🚨 Yes (UI-based alerts)          | 🚨 Yes (Alertmanager integration) |

### 🔐 Security Features
   - User authentication
   - Role-based access
   - HTTPS support

---

## ✨ Summary

Grafana is:
- 📊 Visualization tool
- 🔗 Integrates with Prometheus  
- 🚨 Supports alerting  
- ☸️ Essential for Kubernetes monitoring  

👉 Makes data easy to understand and act on 🚀
