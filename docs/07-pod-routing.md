# Networking

## Routing table between pods in node (copy all content =)))
```bash
SERVER_IP=$(grep server machines.txt | cut -d " " -f 1)
NODE_0_IP=$(grep node-0 machines.txt | cut -d " " -f 1)
NODE_0_SUBNET=$(grep node-0 machines.txt | cut -d " " -f 4)
NODE_1_IP=$(grep node-1 machines.txt | cut -d " " -f 1)
NODE_1_SUBNET=$(grep node-1 machines.txt | cut -d " " -f 4)
```

```bash
ssh root@server <<EOF
  ip route add ${NODE_0_SUBNET} via ${NODE_0_IP}
  ip route add ${NODE_1_SUBNET} via ${NODE_1_IP}
EOF
```
```bash
ssh root@node-0 <<EOF
  ip route add ${NODE_1_SUBNET} via ${NODE_1_IP}
EOF
```
```bash
ssh root@node-1 <<EOF
  ip route add ${NODE_0_SUBNET} via ${NODE_0_IP}
EOF
```

## Config CoreDNS for cluster
```bash
kubectl apply -f https://raw.githubusercontent.com/minhtri6179/k8s-so-hard-huhu/main/configs/coredns-1.8.0.yaml
```

DNS resolution
```bash
kubectl run -it --rm --restart=Never busybox1 --image=busybox sh
---
nslookup google.com
```
and... walaaaaaaaa

![dns](https://raw.githubusercontent.com/minhtri6179/k8s-so-hard-huhu/main/imgs/nslookup.png)