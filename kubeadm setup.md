# Kubeadm Setup on On‑Premises (Bare‑Metal) Servers

## Disable Swap

Kubernetes does not work with swap memory enabled. so we need to disable it.

**Temporary:**

``` bash
sudo swapoff -a
```

**Permanent:**

``` bash
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```

------------------------------------------------------------------------

## Load Required Kernel Modules and Set sysctl Parameters
these commands are compulsory (required) for a successful kubeadm Kubernetes setup — especially on bare
metal or on-premises servers.
> These enable container networking support & help Kubernetes pods talk to each other.
### Run on **both master and worker nodes**.

``` bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
EOF

sudo sysctl --system
```

------------------------------------------------------------------------

## Step 1: Install Docker on All Nodes (master and worker).
Kubernetes requires Docker (or another container runtime) to run containers.

``` bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
```

------------------------------------------------------------------------

## Step 2: Install kubeadm, kubelet, kubectl
- kubeadm: Used to set up the cluster.
- kubelet: Runs on all nodes, ensuring containers are running.
- kubectl: Command-line tool to interact with the cluster.

``` bash
sudo apt update && sudo apt install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list

sudo apt update
sudo apt install -y kubeadm kubelet kubectl
sudo apt-mark hold kubeadm kubelet kubectl
```

------------------------------------------------------------------------

## Step 3: Initialize the Master Node
Once Docker and Kubernetes tools are installed, initialize the Kubernetes cluster on the master node using kubeadm init
``` bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```
- After the master node is initialized, you will see an output with a join command. This command allows worker nodes to join the cluster
- Save the **join command** displayed in the output:

``` bash
kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

------------------------------------------------------------------------

## Step 4: Set Up kubectl on the Master Node
We need to set up kubectl to interact with the Kubernetes cluster from the master node.

``` bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
> 👉 Now, you can interact with the cluster using kubectl.

------------------------------------------------------------------------

## Step 5: Install Calico CNI Plugin (Pod Network) (Master node Only)
To allow pods to communicate, you need a network plugin.
``` bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml
```
👉 Calico creates a network between your pods, so they can talk to each other across different nodes.

------------------------------------------------------------------------

## Step 6: Join Worker Nodes
On the worker nodes, run the join command you saved from Step 3:
Run on worker nodes:

``` bash
kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

👉 To retrieve the join command again If lost:

``` bash
kubeadm token create --print-join-command
```

------------------------------------------------------------------------

## Step 7: Verify the Cluster
#### After joining the worker nodes, verify everything is working correctly
On the master node:

``` bash
kubectl get nodes
```

If all nodes show **Ready**, your on‑prem Kubernetes cluster is successfully set up.
