## Install Kubernetes Cluster using kubeadm:-

This documentation guides you in setting up a cluster with one master node and two worker nodes.

## Assumptions

Role: Master

IP: 167.254.173.122

Hostname:emf53122.nms.fnc.fujitsu.com

OS: CentOS 7

RAM: 2G

CPU: 2

Role: Worker1

IP: 167.254.173.123

Hostname:emf53123.nms.fnc.fujitsu.com

OS: CentOS 7

RAM: 2G

CPU: 2

Role: Worker2

IP: 167.254.173.124

Hostname:emf53124.nms.fnc.fujitsu.com

OS: CentOS 7

RAM: 2G

CPU: 2

## Note: -
On both Master and Worker Perform all the commands as root user unless otherwise specified

#### Disable Firewall

systemctl disable firewalld; systemctl stop firewalld

#### Disable swap

swapoff -a; sed -i '/swap/d' /etc/fstab

#### Disable SELinux

setenforce 0
sed -i --follow-symlinks 's/^SELINUX=enforcing/SELINUX=disabled/' /etc/sysconfig/selinux

#### Update sysctl settings for Kubernetes networking

cat >>/etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system


## Install Docker Engine

yum install -y yum-utils device-mapper-persistent-data lvm2

yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

yum install -y docker-ce-19.03.12 

systemctl enable --now docker


## Kubernetes Setup

Add yum repository

cat >>/etc/yum.repos.d/kubernetes.repo<<EOF

  [kubernetes]

  name=Kubernetes

  baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64

  enabled=1

  gpgcheck=1

  repo_gpgcheck=1

  gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg

  https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg

  EOF

#### Install Kubernetes components

yum install -y kubeadm-1.18.5-0 kubelet-1.18.5-0 kubectl-1.18.5-0

#### Enable and Start kubelet service

systemctl enable --now kubelet

### On master

Initialize Kubernetes Cluster

kubeadm init --apiserver-advertise-address=172.16.16.100 --pod-network-cidr=192.168.0.0/16

### Deploy Calico network

kubectl --kubeconfig=/etc/kubernetes/admin.conf create -f https://docs.projectcalico.org/v3.14/manifests/calico.yaml



## Cluster join command

kubeadm token create --print-join-command


### On worker1 & worker2
 
Join the cluster

Use the output from “__kubeadm token create__ “ command in previous step from the master server and run here.


#### Verifying the cluster:

Get Nodes status

kubectl get nodes

Get component status

kubectl get cs



