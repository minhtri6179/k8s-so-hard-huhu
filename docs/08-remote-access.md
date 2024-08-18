# Remote access
## This is the copy of the original
In this step, I used the port forwarding from master node to this jump host node
```bash
kubectl config set-cluster kubernetes-so-hard-huhu \
	--certificate-authority=ca.crt \
	--embed-certs=true \
	--server=https://master-151.sre.local:6443

kubectl config set-credentials admin \
	--client-certificate=master-151.crt \
	--client-key=master-151.key

kubectl config set-context kubernetes-so-hard-huhu \
	--cluster=kubernetes-so-hard-huhu \
	--user=admin

kubectl config use-context kubernetes-so-hard-huhu
```