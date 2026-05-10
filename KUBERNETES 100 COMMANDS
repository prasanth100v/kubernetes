## 🚀 THE KUBERNETES 100 COMMANDS — Ultimate Cheat Sheet

| 🎯 Category              | ⚡ Command                                                                             | 🔍 Purpose                         |
| ------------------------ | ------------------------------------------------------------------------------------- | ---------------------------------- |
| 🌐 Cluster Info          | `kubectl cluster-info`                                                                | Display master & service addresses |
| 🌐 Cluster Info          | `kubectl config view`                                                                 | Show kubeconfig settings           |
| 🌐 Cluster Info          | `kubectl config get-contexts`                                                         | List cluster contexts              |
| 🌐 Cluster Info          | `kubectl config use-context <name>`                                                   | Switch cluster context             |
| 🌐 Cluster Info          | `kubectl config set-context --current --namespace=<ns>`                               | Set default namespace              |
| 🌐 Cluster Info          | `kubectl api-resources`                                                               | List API resources                 |
| 🌐 Cluster Info          | `kubectl api-versions`                                                                | Show API versions                  |
| 🌐 Cluster Info          | `kubectl get events -A --sort-by='.metadata.creationTimestamp'`                       | 📌 Global cluster events           |
| 🖥️ Node Management      | `kubectl get nodes -o wide`                                                           | List node details                  |
| 🖥️ Node Management      | `kubectl describe node <node>`                                                        | Inspect node resources             |
| 🖥️ Node Management      | `kubectl top nodes`                                                                   | Node CPU/Memory usage              |
| 🖥️ Node Management      | `kubectl cordon <node>`                                                               | Mark node unschedulable            |
| 🖥️ Node Management      | `kubectl uncordon <node>`                                                             | Enable scheduling again            |
| 🖥️ Node Management      | `kubectl drain <node> --ignore-daemonsets --force`                                    | 🚀 Evict pods for maintenance      |
| 🖥️ Node Management      | `kubectl taint node <node> key=value:NoSchedule`                                      | Apply taint                        |
| 🖥️ Node Management      | `kubectl label node <node> disktype=ssd`                                              | Add node label                     |
| 📦 Pod Operations        | `kubectl get pods -A`                                                                 | List all pods                      |
| 📦 Pod Operations        | `kubectl get pods -o wide`                                                            | Show pod IP & node                 |
| 📦 Pod Operations        | `kubectl get pods --show-labels`                                                      | Display pod labels                 |
| 📦 Pod Operations        | `kubectl get pods -l app=nginx`                                                       | Filter by label                    |
| 📦 Pod Operations        | `kubectl describe pod <pod>`                                                          | Pod details/events                 |
| 📦 Pod Operations        | `kubectl logs <pod>`                                                                  | View logs                          |
| 📦 Pod Operations        | `kubectl logs -f <pod>`                                                               | Stream logs                        |
| 📦 Pod Operations        | `kubectl logs <pod> -c <container>`                                                   | Multi-container logs               |
| 📦 Pod Operations        | `kubectl logs -p <pod>`                                                               | 🔥 Previous crashed logs           |
| 📦 Pod Operations        | `kubectl exec -it <pod> -- /bin/sh`                                                   | Open shell inside pod              |
| 📦 Pod Operations        | `kubectl top pods -n <namespace>`                                                     | Pod resource usage                 |
| 📦 Pod Operations        | `kubectl port-forward pod/<pod> 8080:80`                                              | Forward pod port                   |
| 📦 Pod Operations        | `kubectl delete pod <pod> --grace-period=0 --force`                                   | ⚠️ Force delete pod                |
| 📦 Pod Operations        | `kubectl run temp --image=busybox -it --rm -- restart`                                | Temp debug pod                     |
| 📦 Pod Operations        | `kubectl cp <pod>:/path /local/path`                                                  | Copy files from pod                |
| 🚀 Deployments           | `kubectl get deploy`                                                                  | List deployments                   |
| 🚀 Deployments           | `kubectl describe deploy <deploy>`                                                    | Deployment details                 |
| 🚀 Deployments           | `kubectl scale deploy <deploy> --replicas=3`                                          | Scale replicas                     |
| 🚀 Deployments           | `kubectl rollout status deploy/<deploy>`                                              | Rollout status                     |
| 🚀 Deployments           | `kubectl rollout history deploy/<deploy>`                                             | Rollout history                    |
| 🚀 Deployments           | `kubectl rollout undo deploy/<deploy>`                                                | 🔥 Rollback deployment             |
| 🚀 Deployments           | `kubectl rollout restart deploy/<deploy>`                                             | Restart deployment                 |
| 🚀 Deployments           | `kubectl set image deploy/<deploy> <container>=<image>`                               | Update image                       |
| 🚀 Deployments           | `kubectl set resources deploy/<deploy> -c=<container> --limits=cpu=200m,memory=512Mi` | Update resources                   |
| 🚀 Deployments           | `kubectl autoscale deploy <deploy> --min=2 --max=10 --cpu-percent=80`                 | Create HPA                         |
| 🚀 Deployments           | `kubectl get rs`                                                                      | List ReplicaSets                   |
| 🚀 Deployments           | `kubectl delete deploy <deploy>`                                                      | Delete deployment                  |
| 🌍 Services & Networking | `kubectl get svc`                                                                     | List services                      |
| 🌍 Services & Networking | `kubectl describe svc <svc>`                                                          | Service details                    |
| 🌍 Services & Networking | `kubectl get endpoints <svc>`                                                         | 🔥 Verify service routing          |
| 🌍 Services & Networking | `kubectl expose deploy <deploy> --port=80 --target-port=8080`                         | Expose deployment                  |
| 🌍 Services & Networking | `kubectl get ingress`                                                                 | List ingress rules                 |
| 🌍 Services & Networking | `kubectl describe ingress <ingress>`                                                  | Ingress details                    |
| 🌍 Services & Networking | `kubectl get netpol`                                                                  | List NetworkPolicies               |
| 🌍 Services & Networking | `kubectl describe netpol <netpol>`                                                    | Netpol rules                       |
| 🌍 Services & Networking | `kubectl create svc clusterip my-svc --tcp=5678:8080`                                 | Create ClusterIP                   |
| 🌍 Services & Networking | `kubectl create svc loadbalancer my-svc --tcp=80:8080`                                | Create LoadBalancer                |
| 🌍 Services & Networking | `kubectl port-forward svc/<svc> 8080:80`                                              | Port-forward service               |
| 🔐 ConfigMaps & Secrets  | `kubectl get cm`                                                                      | List ConfigMaps                    |
| 🔐 ConfigMaps & Secrets  | `kubectl describe cm <cm>`                                                            | View ConfigMap                     |
| 🔐 ConfigMaps & Secrets  | `kubectl create cm <cm> --from-literal=key=value`                                     | Create ConfigMap                   |
| 🔐 ConfigMaps & Secrets  | `kubectl create cm <cm> --from-file=config.txt`                                       | ConfigMap from file                |
| 🔐 ConfigMaps & Secrets  | `kubectl get secret`                                                                  | List secrets                       |
| 🔐 ConfigMaps & Secrets  | `kubectl describe secret <secret>`                                                    | Secret metadata                    |
| 🔐 ConfigMaps & Secrets  | `kubectl get secret <secret> -o jsonpath='{.data.key}' \| base64 --decode`            | Decode secret                      |
| 🔐 ConfigMaps & Secrets  | `kubectl create secret generic <secret> --from-literal=pass=123`                      | Create secret                      |
| 🔐 ConfigMaps & Secrets  | `kubectl create secret tls <secret> --cert=cert.crt --key=key.key`                    | Create TLS secret                  |
| 🔐 ConfigMaps & Secrets  | `kubectl create secret docker-registry <name> --docker-username=...`                  | Docker registry secret             |
| 💾 Storage               | `kubectl get pv`                                                                      | List PersistentVolumes             |
| 💾 Storage               | `kubectl describe pv <pv>`                                                            | PV details                         |
| 💾 Storage               | `kubectl get pvc`                                                                     | List PVCs                          |
| 💾 Storage               | `kubectl describe pvc <pvc>`                                                          | PVC binding info                   |
| 💾 Storage               | `kubectl get sc`                                                                      | List StorageClasses                |
| 💾 Storage               | `kubectl describe sc <sc>`                                                            | StorageClass details               |
| 💾 Storage               | `kubectl patch pvc <pvc> -p '{"spec":{"resources":{"requests":{"storage":"10Gi"}}}}'` | Expand PVC                         |
| 🔒 RBAC & Security       | `kubectl get sa`                                                                      | List ServiceAccounts               |
| 🔒 RBAC & Security       | `kubectl get roles`                                                                   | List Roles                         |
| 🔒 RBAC & Security       | `kubectl get rolebindings`                                                            | List RoleBindings                  |
| 🔒 RBAC & Security       | `kubectl get clusterroles`                                                            | List ClusterRoles                  |
| 🔒 RBAC & Security       | `kubectl auth can-i create deployments --as=system:serviceaccount:default:my-sa`      | 🔥 Check permissions               |
| 🔒 RBAC & Security       | `kubectl get ns`                                                                      | List namespaces                    |
| 🔒 RBAC & Security       | `kubectl create ns <ns>`                                                              | Create namespace                   |
| 🔒 RBAC & Security       | `kubectl get resourcequotas`                                                          | Namespace quota                    |
| 🔒 RBAC & Security       | `kubectl get limitrange`                                                              | Default resource limits            |
| ⚙️ Declarative Ops       | `kubectl apply -f manifest.yaml`                                                      | Apply YAML                         |
| ⚙️ Declarative Ops       | `kubectl apply -f ./dir/`                                                             | Apply folder                       |
| ⚙️ Declarative Ops       | `kubectl diff -f manifest.yaml`                                                       | 🔥 Preview changes                 |
| ⚙️ Declarative Ops       | `kubectl delete -f manifest.yaml`                                                     | Delete resources                   |
| ⚙️ Declarative Ops       | `kubectl get pod <pod> -o yaml`                                                       | Export YAML                        |
| ⚙️ Declarative Ops       | `kubectl get pod <pod> -o json`                                                       | Export JSON                        |
| ⚙️ Declarative Ops       | `kubectl get pods -o custom-columns=NAME:.metadata.name,IP:.status.podIP`             | Custom columns                     |
| ⚙️ Declarative Ops       | `kubectl explain pod.spec.containers`                                                 | Built-in docs                      |
| ⚙️ Declarative Ops       | `kubectl patch deploy <deploy> -p '{"spec":{"paused":true}}'`                         | Pause rollout                      |
| ⚙️ Declarative Ops       | `kubectl replace --force -f manifest.yaml`                                            | Force recreate                     |
| ⚙️ Declarative Ops       | `kubectl apply -k ./dir/`                                                             | Apply Kustomize                    |

