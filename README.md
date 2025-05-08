# 🧪 Raspberry Pi 5 Kubernetes Cluster (kubeadm - 4 Nodes)

This project sets up a Kubernetes cluster using **4 Raspberry Pi 5s**. One Pi acts as a **dedicated control plane (master)** and the remaining three as **worker nodes**:

- `master-node`: Kubernetes control plane (no workloads)
- `dev-node`: Development
- `qa-node`: QA
- `prod-node`: Production

This setup follows Kubernetes best practices by isolating the control plane.

---
