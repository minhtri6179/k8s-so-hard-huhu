# Setup Jump Host and provisioning cluster state

## Setup Jump Host

<b>What is jump host?</b>
A jump host is a specialized server that plays a critical role in managing and provisioning a cluster. It is responsible for providing essential resources such as binary files and configuration files, as well as generating self-signed certificates to ensure secure communication within the cluster. Additionally, the jump host distributes these resources to all the nodes in the cluster, ensuring they are properly configured and ready for operation. By centralizing these tasks, the jump host simplifies the overall process of provisioning and maintaining the cluster environment, making it an indispensable component in the infrastructure.


> Note: all command in this setup will be run in <b>jump host</b> machine.

## Install necessary package

```bash
apt update
apt install -y install wget curl vim openssl git
```

We are in the process of cloning the repository; however, the current version is configured to work with an ARM64 architecture. Since our lab setup is based on the x86 architecture, we will need to make some modifications to the binary package to ensure compatibility. This step consist of clone repo and download binary.

> Note: If you use x86, use my download package at configs/download.txt instead

```bash
git clone https://github.com/kelseyhightower/kubernetes-the-hard-way.git
wget -q --show-progress \
  --https-only \
  --timestamping \
  -P downloads \
  -i downloads.txt
```
Grant permission for binary k8s components. In the next session, we use kubectl in jump host to control our k8s cluster. 

```bash
chmod +x downloads/kubectl
cp downloads/kubectl /usr/local/bin
```

## Provisioning cluster

To easy mange cluster, we create file store infomation about 3 machine in cluster like that. This file format: `ip-address` `FQDN` `hostname` `ip-range-pod`.

```text
cat machine.txt

10.164.79.151 master-151.sre.local master-151
10.164.79.152 worker-152.sre.local worker-152 10.200.0.0/24
10.164.79.154 worker-154.sre.local worker-154 10.200.1.0/24
```

Config SSH from `jump host` to machines in cluster, the first step is enable root access from ssh. Edit file `/etc/ssh/sshd_config`

```bash
sed -i \
  's/^#PermitRootLogin.*/PermitRootLogin yes/' \
  /etc/ssh/sshd_config

systemctl restart sshd
```

After perform root access, we generate ssh-key from `jump-host` and distribute it to cluster. This command is copy ssh and put to all node in `machine.txt` file.

```bash
ssh-keygen
while read IP FQDN HOST SUBNET; do 
  ssh-copy-id root@${IP}
done < machines.txt
```

Config `hostname` for each machine
```bash
while read IP FQDN HOST SUBNET; do 
    CMD="sed -i 's/^127.0.1.1.*/127.0.1.1\t${FQDN} ${HOST}/' /etc/hosts"
    ssh -n root@${IP} "$CMD"
    ssh -n root@${IP} hostnamectl hostname ${HOST}
done < machines.txt
```

Config DNS `/etc/hosts` for each machine. We create a file name `hosts`. We append these record to `/etc/hosts` for all node in architecture, include this `jump host`.

```text
# Kubernetes so hard ;((
10.164.79.151 master-151.sre.local master-151
10.164.79.152 worker-152.sre.local worker-152
10.164.79.154 worker-154.sre.local worker-154
```
```bash
cat hosts >> /etc/hosts
while read IP FQDN HOST SUBNET; do
  scp hosts root@${HOST}:~/
  ssh -n \
    root@${HOST} "cat hosts >> /etc/hosts"
done < machines.txt
```