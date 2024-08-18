# Bootstrap master node

## Provisioning resources for master node
```bash
scp \
	downloads/etcd-v3.5.13-linux-amd64.tar.gz \
  units/etcd.service \
  downloads/kube-apiserver \
  downloads/kube-controller-manager \
  downloads/kube-scheduler \
  downloads/kubectl \
  units/kube-apiserver.service \
  units/kube-controller-manager.service \
  units/kube-scheduler.service \
  configs/kube-scheduler.yaml \
  configs/kube-apiserver-to-kubelet.yaml \
  root@master-151:~/
```

### Config on node via SSH

### ETCD
```bash
tar -xvf etcd-v3.5.13-linux-amd64.tar.gz
mv etcd-v3.5.13-linux-amd64/etcd* /usr/local/bin/
mkdir -p /etc/etcd /var/lib/etcd
chmod 700 /var/lib/etcd
cp ca.crt kube-api-server.key kube-api-server.crt /etc/etcd/
mv etcd.service /etc/systemd/system/


```

### API server
```bash
mkdir -p /etc/kubernetes/config
chmod +x kube-apiserver \
kube-controller-manager \
kube-scheduler kubectl
mv kube-apiserver \
kube-controller-manager \
kube-scheduler kubectl \
/usr/local/bin/
mkdir -p /var/lib/kubernetes/
mv ca.crt ca.key \
kube-api-server.key kube-api-server.crt \
service-accounts.key service-accounts.crt \
encryption-config.yaml \
/var/lib/kubernetes/
mv kube-apiserver.service /etc/systemd/system/kube-apiserver.service
```
### Control Manager
```bash
mv kube-controller-manager.kubeconfig /var/lib/kubernetes/
mv kube-controller-manager.service /etc/systemd/system/
```

### Kube scheduler
```bash
mv kube-scheduler.kubeconfig /var/lib/kubernetes/
mv kube-scheduler.yaml /etc/kubernetes/config/
mv kube-scheduler.service /etc/systemd/system/
```

## Start master components as `systemd` mode

```bash
systemctl daemon-reload
  
systemctl enable kube-apiserver \
kube-controller-manager kube-scheduler etcd

systemctl start kube-apiserver \
kube-controller-manager kube-scheduler etcd
```