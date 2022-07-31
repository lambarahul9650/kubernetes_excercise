# Kubernetes Installation on Ubuntu 20.04 on AWS

## Prerequisite

1. Change the hostnames

On master - sudo hostnamectl set-hostname k8master
On worker1 -sudo hostnamectl set-hostname worker1

2. Update the Security groups and enable all web traffic from the security group

3. ping the IP address for the reachability

4. Update the /etc/hosts file for the required nameservers

172.31.47.194 worker1
172.31.45.42 k8master
 
ping the hostname , for example from k8master ping worker1 it should work.


ubuntu@ip-172-31-45-42:~$ ping worker1
PING worker1 (172.31.47.194) 56(84) bytes of data.
64 bytes from worker1 (172.31.47.194): icmp_seq=1 ttl=64 time=0.406 ms


5. Disable swap on both the nodes

ubuntu@ip-172-31-45-42:~$ sudo swapoff -a
ubuntu@ip-172-31-45-42:~$ sudo sed -i '/swap/d' /etc/fstab
ubuntu@ip-172-31-45-42:~$

6. Enable IP networking 

cat >>/etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system


7. Install docker
 
apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
apt update
apt install -y docker-ce=5:19.03.10~3-0~ubuntu-focal containerd.io

Change the docker driver to systemd

sudo vi /etc/docker/daemon.json

paste the following 

{
  "exec-opts": ["native.cgroupdriver=systemd"]
}



8. Install Kubernetes

 curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
  echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list

apt update && apt install -y kubeadm=1.20.5-00 kubelet=1.20.5-00 kubectl=1.20.5-00


9. Initialize the K8 master (only on the master node)

sudo kubeadm init --pod-network-cidr=10.244.0.0/16

Once initalize copy the config for user to access the node
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

10. Check the containers on the master

sudo docker ps -a


ubuntu@ip-172-31-45-42:~$ sudo docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED              STATUS              PORTS     NAMES
f6807e4674a3   46e2cd1b2594           "/usr/local/bin/kube…"   About a minute ago   Up About a minute             k8s_kube-proxy_kube-proxy-m59qb_kube-system_f940cb8e-011b-4633-95f9-677da29a041c_0
9fc4f521baf0   k8s.gcr.io/pause:3.2   "/pause"                 About a minute ago   Up About a minute             k8s_POD_kube-proxy-m59qb_kube-system_f940cb8e-011b-4633-95f9-677da29a041c_0
3f7183692a74   9155e4deabb3           "kube-scheduler --au…"   About a minute ago   Up About a minute             k8s_kube-scheduler_kube-scheduler-k8master_kube-system_e8f872f9a07112e96e684366d7248982_0
677b481e76d0   d6296d0e06d2           "kube-controller-man…"   About a minute ago   Up About a minute             k8s_kube-controller-manager_kube-controller-manager-k8master_kube-system_4cf6e9584acbd239de48e3e6bee0bdb7_0
d7b9844b6bfb   k8s.gcr.io/pause:3.2   "/pause"                 About a minute ago   Up About a minute             k8s_POD_kube-controller-manager-k8master_kube-system_4cf6e9584acbd239de48e3e6bee0bdb7_0
48393a5cba69   k8s.gcr.io/pause:3.2   "/pause"                 About a minute ago   Up About a minute             k8s_POD_kube-scheduler-k8master_kube-system_e8f872f9a07112e96e684366d7248982_0
4f994061eb34   323f6347f5e2           "kube-apiserver --ad…"   About a minute ago   Up About a minute             k8s_kube-apiserver_kube-apiserver-k8master_kube-system_17bfe55cc6cab8568ef4c24d07d1add7_0
f7dff6c38403   k8s.gcr.io/pause:3.2   "/pause"                 About a minute ago   Up About a minute             k8s_POD_kube-apiserver-k8master_kube-system_17bfe55cc6cab8568ef4c24d07d1add7_0
3553c0d225eb   0369cf4303ff           "etcd --advertise-cl…"   About a minute ago   Up About a minute             k8s_etcd_etcd-k8master_kube-system_b77b3d62e2764234c53ca2eb16dcf43a_0
419de45574c5   k8s.gcr.io/pause:3.2   "/pause"                 About a minute ago   Up About a minute             k8s_POD_etcd-k8master_kube-system_b77b3d62e2764234c53ca2eb16dcf43a_0
ubuntu@ip-172-31-45-42:~$ sudo docker images
REPOSITORY                           TAG        IMAGE ID       CREATED         SIZE
k8s.gcr.io/kube-proxy                v1.20.15   46e2cd1b2594   6 months ago    99.7MB
k8s.gcr.io/kube-controller-manager   v1.20.15   d6296d0e06d2   6 months ago    116MB
k8s.gcr.io/kube-apiserver            v1.20.15   323f6347f5e2   6 months ago    122MB
k8s.gcr.io/kube-scheduler            v1.20.15   9155e4deabb3   6 months ago    47.3MB
k8s.gcr.io/etcd                      3.4.13-0   0369cf4303ff   23 months ago   253MB
k8s.gcr.io/coredns                   1.7.0      bfe3a36ebd25   2 years ago     45.2MB
k8s.gcr.io/pause                     3.2        80d28bedfe5d   2 years ago     683kB
ubuntu@ip-172-31-45-42:~$ history



 


11. Use the join command on the worker nodes

You should get the following output.
Run 'kubectl get nodes' on the control-plane to see this node join the cluster.


12. Install the CNI

kubectl apply -f https://github.com/coreos/flannel/raw/master/Documentation/kube-flannel.yml




History for worker node

 1  vi /etc/hosts
    2  ip addr
    3  vi /etc/hosts
    4  hostnamectl set-hostname worker2
    5  ping worker1
    6  ping k8master
    7  cat /etc/jpsts
    8  cat /etc/hosts
    9  apt update
   10  sudo swapoff -a
   11  sudo sed -i '/swap/d' /etc/fstab
   12  cat >>/etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

   13  apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
   14  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
   15  add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   16  apt update
   17  apt install -y docker-ce=5:19.03.10~3-0~ubuntu-focal containerd.io
   18  sudo vi /etc/docker/daemon.json
   19  systemctl restart docker
   20  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
   21  echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list
   22  apt update && apt install -y kubeadm=1.20.5-00 kubelet=1.20.5-00 kubectl=1.20.5-00
   23  systemctl status docker
   24  systemctl status kubelet
   25  history
