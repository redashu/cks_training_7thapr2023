## Revision 

### kube-bench 

<img src="kube.png">

### Installation of kube-bench 

<img src="install.png">

### Download binary and set in Linux PATH 

```
root@ip-172-31-22-49:~# mkdir  /opt/tools 
root@ip-172-31-22-49:~# cd /opt/tools/
root@ip-172-31-22-49:/opt/tools# curl -L https://github.com/aquasecurity/kube-bench/releases/download/v0.6.2/kube-bench_0.6.2_linux_amd64.tar.gz -o kube-bench_0.6.2_linux_amd64.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 7821k  100 7821k    0     0  24.4M      0 --:--:-- --:--:-- --:--:-- 24.4M
root@ip-172-31-22-49:/opt/tools# ls
kube-bench_0.6.2_linux_amd64.tar.gz
root@ip-172-31-22-49:/opt/tools# tar xvzf kube-bench_0.6.2_linux_amd64.tar.gz 
cfg/ack-1.0/config.yaml
cfg/ack-1.0/controlplane.yaml
cfg/ack-1.0/etcd.yaml
cfg/ack-1.0/managedservices.yaml

```

### copy Binary in Linux path variable section 

```
root@ip-172-31-22-49:/opt/tools# ls
cfg  kube-bench  kube-bench_0.6.2_linux_amd64.tar.gz
root@ip-172-31-22-49:/opt/tools# 
root@ip-172-31-22-49:/opt/tools# 
root@ip-172-31-22-49:/opt/tools# mv  kube-bench  /usr/bin/
root@ip-172-31-22-49:/opt/tools# 
root@ip-172-31-22-49:/opt/tools# kube-bench version 
0.6.2

```




