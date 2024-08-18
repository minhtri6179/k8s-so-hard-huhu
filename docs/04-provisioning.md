# Provisioning
This chapter will distribute the configuration into all nodes in cluster

## Distribute cert-key pairs to corresponding machines

Each cert-key contains hostname and use to verify with CA server. Ensure these information correctly. If not the server will reject connection from clients.

```bash
for host in worker-152 worker-154; do
  ssh root@$host mkdir /var/lib/kubelet/
  
  scp ca.crt root@$host:/var/lib/kubelet/
    
  scp $host.crt \
    root@$host:/var/lib/kubelet/kubelet.crt
    
  scp $host.key \
    root@$host:/var/lib/kubelet/kubelet.key
done

scp \
  ca.key ca.crt \
  kube-api-server.key kube-api-server.crt \
  service-accounts.key service-accounts.crt \
  root@master-151:~/
```

## Distribute kubeconfig to cluster
```bash
for host in worker-152 worker-154 do
  ssh root@$host "mkdir /var/lib/{kube-proxy,kubelet}"
  
  scp kube-proxy.kubeconfig \
    root@$host:/var/lib/kube-proxy/kubeconfig \
  
  scp ${host}.kubeconfig \
    root@$host:/var/lib/kubelet/kubeconfig
done


scp admin.kubeconfig \
  kube-controller-manager.kubeconfig \
  kube-scheduler.kubeconfig \
  root@server:~/
```

## Distribute encrypted key to API server
```bash
export ENCRYPTION_KEY=$(head -c 32 /dev/urandom | base64)
---

envsubst < configs/encryption-config.yaml \
  > encryption-config.yaml
---

scp encryption-config.yaml root@master-151:~/
```