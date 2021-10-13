Creating Kubernetes cluster with docker:
1. Disable swap, commit swap on /etc/fstab
```
swapoff -a
```
2.Install docker
```
yum install -y yum-utils

yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
    
yum install -y docker-ce docker-ce-cli containerd.io
```
3. Install docker ruttime fo k8s
```
sudo mkdir /etc/docker
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

sudo systemctl enable docker
sudo systemctl daemon-reload
sudo systemctl restart docker
```
4. Install k8s services
```
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
EOF

sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes

sudo systemctl enable --now kubelet
```
5. Init control-plane-node
```
kubeadm intit --pod-network-cidr $NETWORK --control-plane-endpoint=#HOSTDNSNAME --apiserver-advertise-address=$HOSTIP
```
6.Repeat steps 1-4 for other nodes and join them
```
kubeadm join --token=$JIONTOKEN $CONTROLPALNEHOSTNAME:6443 --discovery-token-ca-cert-hash=sha256:$TOKENHASH
```
