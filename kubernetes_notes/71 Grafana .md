# 📊 Grafana
## 🔍 What is Grafana?
 * Grafana is an **open-source visualization & dashboard tool** used to monitor, analyze, and visualize:
   * 📊 `Metrics`, `Logs` and `Traces`
   * 🔍 Analyze system performance
   * 🚨 Set alerts
   * 📈 Monitor applications

### 🎯 Why Grafana?
 * 🔥 Key Benefits:
   - 📊 Beautiful dashboards
   - 🔌 Supports multiple data sources
   - ⚡ Real-time monitoring
   - 🚨 Built-in alerting
   - ☸️ Perfect for Kubernetes

### 🚀 **Prometheus = Metrics Collection** **Grafana = Visualization + Alerting**
#### 👉 Together they provide full observability.

---

## 📦 Install Grafana (Helm)

```yaml
helm install grafana grafana/grafana
```

## 🔌 Connect Grafana to Prometheus
 * 🪜 Steps:
   * 1️⃣ Go to **Grafana UI → Settings → Data Sources**
   * 2️⃣ Select **Prometheus**
   * 3️⃣ Enter URL:
```hcl
http://prometheus:9090
```
  * 4️⃣ Click **Save & Test** ✅

---

## 🔗 Grafana with Prometheus (Kubernetes Setup)
 * 🔄 Flow:
    * 1️⃣ Prometheus collects metrics
    * 2️⃣ Grafana connects as data sources
    * 3️⃣ Dashboards visualize data
    * 4️⃣ Alerts triggered (`Grafana/Prometheus`)  

## 📊 What is a Panel?
 * A **Panel** is a single visualization unit in Grafana.
 * Types of Panels:
   - 📈 Graph (`CPU usage`)
   - 📊 Bar chart
   - 🔢 Stat (`single value`)
   - 📉 Time-series
   - 👉 Each panel runs a query and displays results.


### 📊 What is a Dashboard?
  * 👉 A Dashboard = `Collection of visual panels`
  * ✔️ Shows: `CPU usage`, `Memory usage` & `Network traffic` and `Pod restarts`
  * 🧠 Common Dashboards
     * Node `CPU & Memory usage` 🖥️
     * Pod resource usage ☸️
     * Application metrics 📡
     * Request `latency & errors` ⚡  

## 🚨 Alerting in Grafana
  * Grafana supports alerting:
  * 🔔 Example:
      * CPU > 80% & Pod restart > 3
      * Threshold-based alerts 🚨
      * Notifications (`Email`, `Slack`) 📩
      * Incident monitoring 🔍  

## 📊 Prometheus Real Use Cases (Kubernetes)
| 🧩 Use Case               | 📌 What to Monitor                           | 💡 Why it Matters                               |
| ------------------------- | -------------------------------------------- | ------------------------------------------------- |
| 🚀 Cluster Monitoring     | 🖥️ Node CPU, memory<br>💾 Disk usage        | 📊 Track overall `cluster health & capacity`     |
| 📦 Pod Monitoring         | 🔄 Pod restarts<br>📈 Resource usage         | 🚨 Detect issues like **CrashLoopBackOff**      |
| 🌐 Application Monitoring | 📊 Request rate<br>⏱️ Latency<br>❌ Errors    | 🔍 Understand `app performance` & `failures`   |
| 🕸️ Microservices (`Istio`) | 🔀 Traffic flow<br>🔁 Retries<br>💥 Failures | 🛡️ Observe `service-to-service communication`  |


## 🌟 Features
  - Beautiful dashboards 🎨  
  - Multiple data sources 🔗  
  - Real-time monitoring ⏱️  
  - Custom queries (`PromQL`, etc.) 🧪  

### 📊 Grafana vs Prometheus

| 🧩 **Feature**        | 📊 **Grafana**                  | 📈 **Prometheus**                              | 🧠 **Explanation**                                       | 
| ---------------------- | ------------------------------- | ---------------------------------------------- | -------------------------------------------------------- |
| 🎯 **Role**           | 🎨 Visualization (dashboards)   | 📊 Metrics collection & storage                | Grafana shows data; Prometheus collects it               | 
| 💾 **Data Storage**   | ❌ No (uses data sourceuses external data sources)  | ✅ Yes (time-series DB)    | Prometheus stores metrics; Grafana queries them          |
| 🧪 **Query Language** | 🔎 Uses PromQL (via Prometheus) | 🧠 Provides PromQL                             | Prometheus defines queries; Grafana executes             | 
| 🔔 **Alerting**       | 🚨 Yes (UI-based alerts)         | 🚨 Yes (via Alertmanagerintegration )         | Prometheus handles alert rules; Grafana can also alert   |  
| 🔌 **Data Sources**   | Many (Prometheus, Loki, etc.)   | Mainly its own metrics                          | Grafana is flexible                                       |
| 📡 **Collection**     | ❌ No scraping                   | ✅ Pull-based scraping                        | Prometheus pulls metrics from targets                    |


### 🔐 Security Features
   - User authentication
   - Role-based access
   - HTTPS support

## 📊 Grafana in Kubernetes — Rapid Fire Q&A

| 🔢 Q#   | ❓ Question                                               | 💡 Answer                                                                   |
| ------- | -------------------------------------------------------- | ------------------------------------------------------------------------------ |
| 🔹 Q1   | What is Grafana?                                         | 👉 An open-source `visualization` and analytics tool for monitoring data.    |
| 🔹 Q2   | What does Grafana visualize?                             | 👉 `Metrics`, `logs`, and `traces` from multiple data sources.               |
| 🔹 Q3   | Is Grafana a monitoring tool?                            | 👉 `Not exactly` — it’s a visualization layer (Prometheus does monitoring). |
| 🔹 Q4   | Default Grafana port?                                    | 👉 `3000`                                                                   |
| 🔹 Q5   | Default login credentials?                               | 👉 `admin / admin `(should be changed immediately)                          |
| ☸️ Q6   | How is Grafana deployed in Kubernetes?                   | 👉 Using `Helm charts` or `YAML manifests`.                                  |
| ☸️ Q7   | Popular Helm chart for Grafana?                          | 👉 `kube-prometheus-stack`                                                  |
| ☸️ Q8   | How to expose Grafana externally?                        | 👉 `NodePort`, `LoadBalancer`, or `Ingress`.                                 |
| ☸️ Q9   | Where are Grafana dashboards stored?                     | 👉 In `ConfigMaps` or `persistent storage` (PVC).                             |
| ☸️ Q10  | How to auto-load dashboards in K8s?                      | 👉 Use `sidecar container + ConfigMaps`.                                    |
| 🔌 Q11  | What is a data source in Grafana?                        | 👉 Backend system providing data (e.g., `Prometheus`).                      |
| 🔌 Q12  | Common data sources?                                     | 👉 `Prometheus`, Loki, `Elasticsearch`, `MySQL`.                             |
| 🔌 Q13  | How does Grafana connect to Prometheus?                  | 👉 HTTP endpoint (`[http://prometheus:9090](http://prometheus:9090)`).      |
| 🔌 Q14  | Can Grafana use multiple data sources?                   | 👉 Yes.                                                                   |
| 📊 Q15  | What is a dashboard?                                     | 👉 A collection of panels `visualizing metrics.`                            |
| 📊 Q16  | What is a panel?                                         | 👉 Individual visualization (`graph`, `table`, `gauge`).                     |
| 📊 Q17  | What is a variable in Grafana?                           | 👉 Dynamic value used to filter dashboards.                               |
| 📊 Q18  | Types of panels?                                         | 👉 Graph, Stat, Table, Heatmap, Gauge.                                    |
| 🚨 Q19  | Does Grafana support alerting?                           | 👉 Yes (`Grafana Alerting`).                                                |
| 🚨 Q20  | Difference between Grafana alerts and Prometheus alerts? | 👉 Grafana = `UI-based`, Prometheus = `rule-based`.                         |
| 🚨 Q21  | Alert notification channels?                             | 👉 `Email`, `Slack`, PagerDuty, `Webhooks`.                                |
| 🔐 Q22  | How to secure Grafana in Kubernetes?                     | 👉 Use `Ingress + TLS`, `RBAC`, `OAuth`.                                    |
| 🔐 Q23  | Can Grafana integrate with LDAP/OAuth?                   | 👉 Yes.                                                                   |
| 🔐 Q24  | Role types in Grafana?                                   | 👉 Admin, Editor, Viewer.                                                 |
| 💾 Q25  | Why use Persistent Volume for Grafana?                   | 👉 To `retain dashboards` & settings after restart.                         |
| 💾 Q26  | What happens without persistence?                        | 👉 `Dashboards are lost on pod restart`.                                    |
| ⚙️ Q27  | How to configure Grafana in K8s?                         | 👉 `ConfigMaps`, `Helm values.yaml`.                                          |
| ⚙️ Q28  | What is provisioning in Grafana?                         | 👉 Auto-configuring dashboards & data sources.                            |
| ⚙️ Q29  | Where is provisioning defined?                           | 👉 `/etc/grafana/provisioning/`                                             |
| 🚀 Q30  | Why dashboards load slowly?                              | 👉 `Heavy queries`, `large time range`.                                       |
| 🚀 Q31  | How to improve performance?                              | 👉 Optimize `queries`, `use variables`, reduce refresh rate.                  |
| 📜 Q32  | How to monitor Grafana itself?                           | 👉 Enable /metrics endpoint and scrape with Prometheus.                      |
| 📜 Q33  | How to view Grafana logs?                                | 👉 `kubectl logs <grafana-pod>  `                                           |
| 🛠️ Q34 | Dashboard not loading?                                   | 👉 Check: `Data source connectivity`, `Query errors`, `Time range`           |
| 🛠️ Q35 | Data not visible?                                        | 👉 Check: `Prometheus targets`, `Labels mismatch`                             |
| 🛠️ Q36 | Login issues?                                            | 👉 Reset `admin password` or `check auth config`.                             |
| 🚀 Q37  | What is Loki?                                            | 👉 `Log aggregation` ( process of collecting, combining) system integrated with Grafana.  |
| 🚀 Q38  | What is Tempo?                                           | 👉 Distributed tracing backend.                                           |
| 🚀 Q39  | What is Grafana Agent?                                   | 👉 Lightweight collector for `metrics/logs`.                                |
| 🚀 Q40  | What is multi-tenancy in Grafana?                        | 👉 `Separate orgs` or folders for different teams.                          |
| ☸️ Q41  | How to import dashboards automatically?                  | 👉 Use `ConfigMaps` with `labels` (grafana_dashboard).                     |
| ☸️ Q42  | How to expose Grafana securely?                          | 👉 `Ingress + TLS + authentication`.                                        |
| ☸️ Q43  | How to upgrade Grafana in K8s?                           | 👉 Helm upgrade.                                                          |
| ☸️ Q44  | How to backup dashboards?                                | 👉 Export JSON or use API.                                                |
| 🎯 Q45  | Grafana shows no data, but Prometheus has metrics — why? | 👉` Wrong query`, `label mismatch`, or `datasource issue`.                 |
| 🎯 Q46  | How to handle multiple environments (dev/prod)?          | 👉 Use variables or separate data sources.                                |
| 🎯 Q47  | How to restrict dashboard access?                        | 👉 `RBAC roles` or `folder permissions`.                                   |
| 🎯 Q48  | Why use Grafana over Kibana?                             | 👉 Better for `metrics` ; Kibana is for `logs`.                            |

---

## ✨ Summary

Grafana is:
  - 📊 Visualization tool
  - 🔗 Integrates with Prometheus  
  - 🚨 Supports alerting  
  - ☸️ Essential for Kubernetes monitoring
  - 👉 Makes data easy to understand and act on 🚀
