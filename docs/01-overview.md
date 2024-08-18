# Overview and System Requirement

In this lap setup, we use 4 virtual machine based on x86.

| Name | CPU | RAM | Storage |
| -------- | ------- | -------- | ------- |
| jump host | 4 | 8 | 32 | 
| master-151 | 4 | 8 | 32 |
| worker-152 | 4 | 8 | 32 |
| worker-154 | 4 | 8 | 32 |

---
Master node has: `kube-apiserver`, `kube-controller-manager`, `kube-scheduler`, `etcd`

Each worker node has: `kubelet`, `kube-proxy`, `container runtime`
