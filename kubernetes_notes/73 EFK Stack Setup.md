# 🚀 EFK Stack Setup in Kubernetes
 * To set up the EFK stack in Kubernetes, I used a combination of tools like `Helm`, `DaemonSet`, and `port-forward` to make the setup easier and more efficient.

## 1️⃣ Namespace Creation
 * I created a separate namespace called `logging` to isolate all logging components from other workloads in the cluster.

## 2️⃣ Install Elasticsearch & Kibana using Helm
 * I used Helm to install Elasticsearch and Kibana quickly. Helm charts simplified the deployment process because I didn’t have to write all the YAML manifests manually.
 * Elasticsearch was deployed with **1 replica** for testing purposes.
 * In a production environment, I would use **3 nodes** for `high availability` and `better performance`.

## 3️⃣ Deploy Fluentd as DaemonSet
 * For log collection, I deployed Fluentd as a DaemonSet.
 * This ensures **one Fluentd pod runs on each node**.
 * Fluentd `collects logs` from all containers on that node.
 * It reads logs from `/var/log/containers/`.
 * It enriches logs with Kubernetes metadata like:
    * Pod name
    * Namespace
    * Labels
 * Then it forwards logs to Elasticsearch.

## 4️⃣ Access Kibana Dashboard
 * To access the Kibana UI, I used the `kubectl port-forward` command to expose it locally.
 * This allowed me to open Kibana in my browser at:
    * 👉 [http://localhost:5601](http://localhost:5601)

## 5️⃣ Testing the Setup
 * I tested the setup by deploying an application that generates logs.
 * Verified that Fluentd collects logs
 * Confirmed logs are stored in Elasticsearch
 * Successfully `viewed logs` in Kibana in real time

## 6️⃣ Final Outcome
 * This setup provides:
    * ✅ Centralized log collection
    * 🔍 Easy `log search` and filtering
    * 📊 Visualization through `Kibana`
    * 🛠️ Faster debugging of microservices
    * Overall, the EFK stack makes `monitoring` and `troubleshooting applications` in Kubernetes much easier and more efficient.

## 📝 Final Tip:
  * Be confident and keep your answers simple but accurate. If you don’t know something,
  * say: `“I haven’t worked on that yet, but I’m eager to learn it quickly.”`

