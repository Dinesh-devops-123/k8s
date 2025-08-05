# on-premises setup 1-master and 2-worker node 

🧠 Prerequisites (all nodes)
Do this on master and both workers:

1. Disable Swap
 $ sudo swapoff -a
 $ sudo sed -i '/ swap / s/^/#/' /etc/fstab

2. Kernel Networking Setup
 $ cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
   net.bridge.bridge-nf-call-iptables  = 1
   net.bridge.bridge-nf-call-ip6tables = 1
   net.ipv4.ip_forward                 = 1
   EOF
 $ sudo sysctl --system

3. Install containerd
  $ sudo apt update
  $ sudo apt install -y containerd
  $ sudo mkdir -p /etc/containerd
  $ containerd config default | sudo tee /etc/containerd/config.toml
  $ sudo systemctl restart containerd
  $ sudo systemctl enable containerd

4. Add Kubernetes APT Repo
  $ sudo apt install -y apt-transport-https ca-certificates curl gpg
  $ sudo mkdir -p /etc/apt/keyrings
  $ curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
  $ echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
  $ sudo apt update

5. Install Kubernetes Tools
  $ sudo apt install -y kubelet kubeadm kubectl
  $ sudo apt-mark hold kubelet kubeadm kubectl

🚀 Master Node Setup (e.g., 10.10.12.39)
1. (Optional) Use kubeadm config file for HA/fine-tuned setup
Save as kubeadm-config.yaml:
...
 ```yaml
 # apiVersion: kubeadm.k8s.io/v1beta3
 # kind: ClusterConfiguration
 # kubernetesVersion: v1.33.3
 # controlPlaneEndpoint: "10.10.12.39:6443"
 # networking:
 #  podSubnet: "10.244.0.0/16"
...
2.Initialize Kubernetes Master
  $ sudo kubeadm init --config kubeadm-config.yaml --upload-certs

# If using simple setup without config file:
  $ sudo kubeadm init --pod-network-cidr=10.244.0.0/16

3. Configure kubectl for root user
 $ mkdir -p $HOME/.kube
 $ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
 $ sudo chown $(id -u):$(id -g) $HOME/.kube/config

🌐 Install Cilium CNI (on master)
 # Download Cilium CLI
$ curl -L --remote-name https://github.com/cilium/cilium-cli/releases/latest/download/cilium-linux-amd64.tar.gz
$ sudo tar xzvf cilium-linux-amd64.tar.gz -C /usr/local/bin
$ cilium version

#Option 1: Install Helm (Recommended via Script)
 $curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

Option 2: Install via apt (alternative)
  $sudo apt update
  $sudo apt install -y helm

# Add Cilium Helm repo --Cilium Helm repo yet, do this first:
 $ helm repo add cilium https://helm.cilium.io/
 $ helm repo update

# Install Cilium with Helm
helm install cilium cilium/cilium \
  --version 1.17.5 \
  --namespace kube-system \
  --set cluster.name=kubernetes \
  --set cluster.id=1 \
  --set ipam.mode=cluster-pool \
  --set clusterPoolIPv4PodCIDR=10.244.0.0/24 \
  --set clusterPoolIPv4MaskSize=24 \
  --set routingMode=native \
  --set ipv4NativeRoutingCIDR=10.10.12.0/24 \
  --set devices=enX0 \
  --set kubeProxyReplacement=true \
  --set bpf.nodePort.enabled=true \
  --set hostPort.enabled=true \
  --set nodeinit.enabled=true \
  --set hubble.enabled=true \
  --set hubble.relay.enabled=true \
  --set hubble.ui.enabled=true \
  --wait

#Optional: Access Cilium Hubble UI
 $ kubectl -n kube-system patch svc hubble-ui -p '{"spec": {"type": "NodePort"}}'
 $ kubectl -n kube-system get svc hubble-ui

🧑‍🤝‍🧑 Worker Node Setup (run on each worker)
1. Join command (from master)
 Get this from master:
  $ kubeadm token create --print-join-command

 Run on each worker node:
  $ sudo kubeadm join <MASTER_IP>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>

# OR if using config file worker-join.yaml:
  apiVersion: kubeadm.k8s.io/v1beta3
  kind: JoinConfiguration
  discovery:
  bootstrapToken:
    token: <token>
    apiServerEndpoint: "10.10.12.39:6443"
    caCertHashes:
    - sha256:<hash>

  $ sudo kubeadm join --config worker-join.yaml

✅ Post Setup (on master)
1. Un-taint master if it should run pods
 $ kubectl taint nodes master1 node-role.kubernetes.io/control-plane- --overwrite

2. Label worker nodes (optional)
  $ kubectl label node worker1 node-role.kubernetes.io/worker=
  $ kubectl label node worker2 node-role.kubernetes.io/worker=

3. Verify Cluster Status
    $ kubectl get nodes -o wide
    $ kubectl get pods -A

🧪 Troubleshooting
View kubelet logs:
 $ sudo journalctl -xeu kubelet

Cilium status:
 $ cilium status

