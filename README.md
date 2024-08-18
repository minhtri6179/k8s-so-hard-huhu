# k8s-so-hard-huhu
This is my learning journal for deploying Kubernetes cluster on virtual machines.


Thanks to the [kubernetes-the-hard-way](https://github.com/kelseyhightower/kubernetes-the-hard-way)

This guide for virtual machine with AMD64 (Ubuntu) architecture. Contains the scripts and simple explanation for each components.

![dns](https://raw.githubusercontent.com/minhtri6179/k8s-so-hard-huhu/main/imgs/nslookup.png)

## Chapters
1. [Overview and System Requirement](/docs/01-overview.md)
2. [Setup Jump Host and provisioning cluster state](/docs/02-setup-jump-host.md)
3. [Generate Own CA and self-signed certificates](/docs/03-ca-cert.md)
4. [Provisioning and configuring](/docs/04-provisioning.md)
5. [Bootstrapping master node](/docs/05-master-node.md)
6. [Bootstrapping worker nodes](/docs/06-worker-nodes.md)
7. [Pod routing and DNS resolution](/docs/07-pod-routing.md)
8. [Remote access from jump host](/docs/08-remote-access.md)