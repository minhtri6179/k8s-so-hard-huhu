# Bootstrap worker nodes
## Provisioning resources for worker nodes
```bash
for host in worker-152 worker-154; do
  SUBNET=$(grep $host machines.txt | cut -d " " -f 4)
  sed "s|SUBNET|$SUBNET|g" \
    configs/10-bridge.conf > 10-bridge.conf 
    
  sed "s|SUBNET|$SUBNET|g" \
    configs/kubelet-config.yaml > kubelet-config.yaml
    
  scp 10-bridge.conf kubelet-config.yaml \
  root@$host:~/
done
```

```bash
for host in node-0 node-1; do
  scp \
    downloads/runc.arm64 \
    downloads/crictl-v1.29.0-linux-amd64.tar.gz \
    downloads/cni-plugins-linux-amd64-v1.4.0.tgz \
    downloads/containerd-1.7.15-linux-amd64.tar.gz \
    downloads/kubectl \
    downloads/kubelet \
    downloads/kube-proxy \
    configs/99-loopback.conf \
    configs/containerd-config.toml \
    configs/kube-proxy-config.yaml \
    units/containerd.service \
    units/kubelet.service \
    units/kube-proxy.service \
    root@$host:~/
done
```
## Config each worker node via SSH command

```bash
apt-get update
apt-get -y install socat conntrack ipset
```

> [Disable permanently](https://askubuntu.com/questions/440326/how-can-i-turn-off-swap-permanently)
> reboot and check again to ensure swap is disabled permanently

```bash
mkdir -p \
  /etc/cni/net.d \
  /opt/cni/bin \
  /var/lib/kubelet \
  /var/lib/kube-proxy \
  /var/lib/kubernetes \
  /var/run/kubernetes
```

```bash
mkdir -p containerd
  tar -xvf crictl-v1.29.0-linux-amd64.tar.gz
  tar -xvf containerd-1.7.15-linux-amd64.tar.gz -C containerd
  tar -xvf cni-plugins-linux-amd64-v1.4.0.tgz -C /opt/cni/bin/
  mv runc.arm64 runc
  chmod +x crictl kubectl kube-proxy kubelet runc 
  mv crictl kubectl kube-proxy kubelet runc /usr/local/bin/
  mv containerd/bin/* /bin/
```

```bash
mv 10-bridge.conf 99-loopback.conf /etc/cni/net.d/
```

`containerd` as the container runtime
```bash
mkdir -p /etc/containerd/
mv containerd-config.toml /etc/containerd/config.toml
mv containerd.service /etc/systemd/system/
```
`kubelet`
```bash
mv kubelet-config.yaml /var/lib/kubelet/
mv kubelet.service /etc/systemd/system/
```
`kube proxy`
```bash
mv kube-proxy-config.yaml /var/lib/kube-proxy/
mv kube-proxy.service /etc/systemd/system/
```
## Start these components as `systemd` and enjoy =))
```bash
systemctl daemon-reload
systemctl enable containerd kubelet kube-proxy
systemctl start containerd kubelet kube-proxy
```