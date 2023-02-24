# Creating k8s cluster w/ kubeadm

## Details

![image](https://user-images.githubusercontent.com/12085451/221173478-1fb37ee2-9a06-4cec-93a5-e99c023d3c65.png)

![image](https://user-images.githubusercontent.com/12085451/221173556-cedd9299-46c5-40b3-ac34-4600d40bd388.png)

> ubuntu instances used for lab nodes

## Control plane 

```sh
cloud_user@k8s-control:~$ history
    1  cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF
    2  sudo modprobe overlay
    3  sudo modprobe br_netfilter
    4  cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
    5  sudo sysctl --system
    6  sudo apt-get update && sudo apt-get install -y containerd.io
    7  sudo mkdir -p /etc/containerd
    8  sudo containerd config default | sudo tee /etc/containerd/config.toml
    9  sudo systemctl restart containerd
   10  sudo systemctl status containerd
   11  sudo swapoff -a
   12  sudo apt-get update && sudo apt-get install -y apt-transport-https curl
   13  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
   14  cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
   15  sudo apt-get update
   16  sudo apt-get install -y kubelet=1.24.0-00 kubeadm=1.24.0-00 kubectl=1.24.0-00
   17  sudo kubeadm init --pod-network-cidr 192.168.0.0/16 --kubernetes-version 1.24.0
   18  kubectl get nodes
   19  mkdir -p $HOME/.kube
   20  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   21  sudo chown $(id -u):$(id -g) $HOME/.kube/config
   22  kubectl get nodes
   23  kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
   24  watch kubectl get nodes
   25  kubectl describe node k8s-control
   26  watch kubectl get nodes
   27  kubectl get pods
   28  kubectl get pods -A
   29  kubeadm token create --print-join-command
   30  watch kubectl get nodes -o wide
```


## Worker nodes

```sh
cloud_user@k8s-worker1:~$ history
    1  cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF
    2  sudo modprobe overlay
    3  sudo modprobe br_netfilter
    4  cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
    5  sudo sysctl --system
    6  sudo apt-get update && sudo apt-get install -y containerd.io
    7  sudo mkdir -p /etc/containerd
    8  sudo containerd config default | sudo tee /etc/containerd/config.toml
    9  sudo systemctl restart containerd
   10  sudo systemctl status containerd
   11  sudo swapoff -a
   12  sudo apt-get update && sudo apt-get install -y apt-transport-https curl
   13  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
   14  cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
   15  sudo apt-get update
   16  sudo apt-get install -y kubelet=1.24.0-00 kubeadm=1.24.0-00 kubectl=1.24.0-00
   17  sudo kubeadm join 10.0.1.101:6443 --token 1wdd1m.s5pwmzu7eaz0yaz8 --discovery-token-ca-cert-hash sha256:b8b187438fd033a65e4667d370bf60138e7ac2b7976a4b7ae2e4cd6182a3a966 
```
