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