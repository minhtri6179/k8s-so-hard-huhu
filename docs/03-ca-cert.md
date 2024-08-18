# Generate Own CA and self-signed certificates
This chapter create own CA and self-signed certificates

## Own CA 
> This is own CA, we should add trust CA into OS to avoid the `alert unknown ca`.

Generate CA: Because we have custom hostname, we need to config ca.conf from repo
```bash
openssl genrsa -out ca.key 4096
openssl req -x509 -new -sha512 -noenc \
    -key ca.key -days 3653 \
    -config ca.conf \
    -out ca.crt
```

Generate master-151, worker-152, worker-154 and component k8s cert-key pairs from own root CA
```bash
certs=(
  "master-151" "worker-152" "worker-154"
  "kube-proxy" "kube-scheduler"
  "kube-controller-manager"
  "kube-api-server"
  "service-accounts"
)

for i in ${certs[*]}; do
  openssl genrsa -out "${i}.key" 4096

  openssl req -new -key "${i}.key" -sha256 \
    -config "ca.conf" -section ${i} \
    -out "${i}.csr"
  
  openssl x509 -req -days 3653 -in "${i}.csr" \
    -copy_extensions copyall \
    -sha256 -CA "ca.crt" \
    -CAkey "ca.key" \
    -CAcreateserial \
    -out "${i}.crt"
done
```

### Distribute cert-key pairs to corresponding machines

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