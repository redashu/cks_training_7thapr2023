# cks_training_7thapr2023

### GIthu profile ashutoshh 

[click_here](https://github.com/redashu/CKS.git)
====

```
https://github.com/redashu/CKS.git
```

## Feedback 

[click_here](https://www.metricsthatmatter.com/url/u.aspx?BF9D6E70A196018328)


### setup 2 node k8s 1.25 in ubuntu 20.04

## Steps to perform in both VM 

### step 1 

```
 40  apt-get install -y apt-transport-https ca-certificates curl
   41  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
   42  echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
   43  vim /etc/sysctl.d/k8s.conf
   44  history 
   45  vim /etc/modules-load.d/containerd.conf
   46  modprobe br_netfilter
   47  modprobe overlay 
   48  apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
   49  url -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   50  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   51  sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   52  apt update
   53  apt install -y containerd.io
   54  mkdir -p /etc/containerd
   55  systemctl enable containerd
   56  apt install  kubeadm=1.25* kubelet=1.25*  kubectl=1.25*
   57  kubeadm config images pull --cri-socket unix:///run/containerd/containerd.sock
```

### control plan setup  only in control plane vm 

```
 kubeadm  init --pod-network-cidr=192.168.0.0/16
 
 root@ip-172-31-90-26:~# mkdir -p $HOME/.kube
root@ip-172-31-90-26:~#  cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
root@ip-172-31-90-26:~# kubectl get nodes
NAME              STATUS     ROLES           AGE   VERSION
ip-172-31-90-26   NotReady   control-plane   27s   v1.25.8
root@ip-172-31-90-26:~# 


```

### you will get message like given below you can paste in worker node you wnat to create 

```

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.31.90.26:6443 --token rsvzje.qthqqbuu0r6thhce \
	--discovery-token-ca-cert-hash sha256:c215757cbc8dbc2c3d2f18fafdb901ef20b314b24885bf29882461a650e15205 
```

