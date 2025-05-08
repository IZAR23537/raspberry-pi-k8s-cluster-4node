# 🛠️ Kubernetes Setup on 4 Raspberry Pi 5s (with kubeadm)

This guide sets up a production-style Kubernetes cluster on **4 Raspberry Pi 5s**. One Pi is dedicated to the control plane, and three act as worker nodes.

---

## 1. Flash Ubuntu Server to All Raspberry Pis

- Use Raspberry Pi Imager
- Select **Ubuntu Server 22.04 LTS (64-bit)**

## 2. Configure Static IPs and Hostnames

| Hostname     | IP             |
|--------------|----------------|
| master-node  | 192.168.1.100  |
| dev-node     | 192.168.1.101  |
| qa-node      | 192.168.1.102  |
| prod-node    | 192.168.1.103  |

Set `/etc/hostname` and `/etc/netplan/*.yaml` accordingly.

---

## 3. SSH into Each Pi and Run:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y containerd apt-transport-https curl
```

## 4. Disable Swap (Required)

```bash
sudo dphys-swapfile swapoff
sudo systemctl disable dphys-swapfile
```

✅ Verify:
```bash
free -h
# Swap: 0B 0B 0B
```

## 5. Install kubeadm, kubelet, kubectl

```bash
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

## 6. Initialize the Control Plane (on master-node)

```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

Follow the printed instructions to copy the `kubeadm join` command.

Set up `kubectl`:
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## 7. Install Flannel CNI

```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

## 8. Join Worker Nodes

Run the `kubeadm join ...` command on:
- `dev-node`
- `qa-node`
- `prod-node`

## 9. Label Each Node by Environment

```bash
kubectl label node dev-node env=dev
kubectl label node qa-node env=qa
kubectl label node prod-node env=prod
```

✅ Check:
```bash
kubectl get nodes --show-labels
```

---
