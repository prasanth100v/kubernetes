# 🔥 CKA Focused Kubernetes Interview Questions & Answers
## CKA (Certified Kubernetes Administrator)–focused interview questions
```
🧱 Cluster Architecture, Installation & Configuration (25%)
🔍 Workloads & Scheduling (15%)
🌐 Networking (20%)
📦 Storage (10%)
🔐 Security (20%)
🔧 Troubleshooting (30%) ⭐ MOST IMPORTANT
```
### How do you check cluster information?
```
kubectl cluster-info
kubectl get nodes -o wide
```
### How do you check control plane components?
```
kubectl get pods -n kube-system
```
### Where are kubelet logs stored?
```
journalctl -u kubelet
```
### How do you backup etcd?
```
ETCDCTL_API=3 etcdctl snapshot save backup.db \
--endpoints=https://127.0.0.1:2379 \
--cacert=ca.crt \
--cert=server.crt \
--key=server.key
```
### How do you restore etcd?
```
How do you restore etcd?
```
### How do you cordon and drain a node?
```
kubectl cordon node1                            (Prevents new pods)
kubectl drain node1 --ignore-daemonsets         (Evicts existing pods)
```
### How do you run a pod on a specific node?
Using nodeSelector
```
nodeSelector:
  disktype: ssd
```
### What is taint & toleration?
```
kubectl taint nodes node1 key=value:NoSchedule
```
Pod toleration:
```
tolerations:
- key: "key"
  operator: "Equal"
  value: "value"
  effect: "NoSchedule"
```
### How do you check CNI plugin?
```
ls /etc/cni/net.d/
```
### How does kube-proxy work?
Manages service networking using: iptables OR ipvs
### Expose a pod using NodePort
```
kubectl expose pod nginx --type=NodePort --port=80
```
### How to check volume mounted in pod?
```
kubectl describe pod pod-name
```
### Pod stuck in Pending state – reasons?
```
kubectl describe pod
```
### Pod in CrashLoopBackOff – steps?
```
kubectl logs pod
kubectl describe pod
```
### Node NotReady – how to fix?
```
kubectl describe node node1
systemctl status kubelet
```
### How do you check cluster events?
```
kubectl get events -A
```










