## Revision 

### Understand security with RBAC & SSL 

<img src="rbac.png">

### kubeconfig custom file 

<img src="kubeconf.png">

### service account story in kubernetes versions 

<img src="sa.png">

### security alter automount of secret of namespace service account 

<img src="m.png">

### Deploy pod 

```
root@ip-172-31-22-49:~# kubectl run ashupod1 --image=nginx --port 80 --dry-run=client -o yaml  >podx.yaml 
root@ip-172-31-22-49:~# 
root@ip-172-31-22-49:~# kubectl get po
No resources found in default namespace.
root@ip-172-31-22-49:~# kubectl apply -f podx.yaml 
pod/ashupod1 created
root@ip-172-31-22-49:~# kubectl get po 
NAME       READY   STATUS    RESTARTS   AGE
ashupod1   1/1     Running   0          4s
root@ip-172-31-22-49:~# 
```

### checking secret of pod 

```
root@ip-172-31-22-49:~# kubectl exec -it  ashupod1  -- bash 
root@ashupod1:/# 
root@ashupod1:/# 
root@ashupod1:/# cd /var/run
root@ashupod1:/var/run# ls
lock  nginx.pid  secrets  utmp
root@ashupod1:/var/run# cd secrets/
root@ashupod1:/var/run/secrets# ls
kubernetes.io
root@ashupod1:/var/run/secrets# cd kubernetes.io/
root@ashupod1:/var/run/secrets/kubernetes.io# ls
serviceaccount
root@ashupod1:/var/run/secrets/kubernetes.io# ls -l
total 0
drwxrwxrwt 3 root root 140 Apr 11 03:40 serviceaccount
root@ashupod1:/var/run/secrets/kubernetes.io# cd serviceaccount/
root@ashupod1:/var/run/secrets/kubernetes.io/serviceaccount# ls
ca.crt	namespace  token
root@ashupod1:/var/run/secrets/kubernetes.io/serviceaccount# cat token 
eyJhbGciOiJSUzI1NiIsImtpZCI6IkN2QjhrMWtwSHhNbjlLR0xKSmhnc0lIcGRFYUZXYmFtNFlUSVFqeWoyalUifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNzEyNzIwNDU1LCJpYXQiOjE2ODExODQ0NTUsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJkZWZhdWx0IiwicG9kIjp7Im5hbWUiOiJhc2h1cG9kMSIsInVpZCI6IjFmM2JhODM3L
```

### blocking service account mounting 

```
root@ip-172-31-22-49:~# cat  podx.yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: ashupod1
  name: ashupod1
spec:
  automountServiceAccountToken: false # blocking secret mounting 
  containers:
  - image: nginx
    name: ashupod1
    ports:
    - containerPort: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```

### From k8s 1.24 onwards there are no secret token got generate for service account 

### SO if you need this token 

```
root@ip-172-31-22-49:~# kubectl create  sa  app-ok 
serviceaccount/app-ok created
root@ip-172-31-22-49:~# kubectl get  sa
NAME      SECRETS   AGE
app-ok    0         3s
default   0         5d19h
root@ip-172-31-22-49:~# kubectl create token  app-ok 
eyJhbGciOiJSUzI1NiIsImtpZCI6IkN2QjhrMWtwSHhNbjlLR0xKSmhnc0lIcGRFYUZXYmFtNFlUSVFqeWoyalUifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNjgxMTg4NzgzLCJpYXQiOjE2ODExODUxODMsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJkZWZhdWx0Iiwic2VydmljZWFjY291bnQiOnsibmFtZSI6ImFwcC1vayIsInVpZCI6IjI0ZmQ5YTNmLTc2MjItNGU4Yy05ZmIwLTA2MDc5NTIyNjBkMyJ9fSwibmJmIjoxNjgxMTg1MTgzLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6ZGVmYXVsdDphcHAtb2sifQ.oVwtLrru5-od1UBV23q8BHpwBX8g6Q3comF0P4L7PjhUpGac9JDerrz8MYV7q21QjsYDJaEEl7DygElHfmXUsnVIDeqOhDfhEfwqHj0u

```

### See token demo in k8s dashboard 

```
root@ip-172-31-22-49:~# kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
namespace/kubernetes-dashboard created
serviceaccount/kubernetes-dashboard created
service/kubernetes-dashboard created
secret/kubernetes-dashboard-certs created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
configmap/kubernetes-dashboard-settings created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
deployment.apps/kubernetes-dashboard created
service/dashboard-metrics-scraper created
deployment.apps/dashboard-metrics-scraper created
root@ip-172-31-22-49:~# 


```

### lets check it 

```
root@ip-172-31-22-49:~# kubectl get  ns
NAME                   STATUS   AGE
app-test               Active   22h
default                Active   5d19h
kube-node-lease        Active   5d19h
kube-public            Active   5d19h
kube-system            Active   5d19h
kubernetes-dashboard   Active   30s
root@ip-172-31-22-49:~# kubectl  get po -n kubernetes-dashboard 
NAME                                         READY   STATUS    RESTARTS   AGE
dashboard-metrics-scraper-64bcc67c9c-drmt5   1/1     Running   0          47s
kubernetes-dashboard-5c8bd6b59-j7p2w         1/1     Running   0          47s
root@ip-172-31-22-49:~# kubectl  get sa -n kubernetes-dashboard 
NAME                   SECRETS   AGE
default                0         61s
kubernetes-dashboard   0         61s
root@ip-172-31-22-49:~# kubectl  get secret  -n kubernetes-dashboard 
NAME                              TYPE     DATA   AGE
kubernetes-dashboard-certs        Opaque   0      78s
kubernetes-dashboard-csrf         Opaque   1      78s
kubernetes-dashboard-key-holder   Opaque   2      78s
root@ip-172-31-22-49:~# 

```

### to login into dashboard 

```
root@ip-172-31-22-49:~# kubectl get  svc -n kubernetes-dashboard 
NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
dashboard-metrics-scraper   ClusterIP   10.111.75.201    <none>        8000/TCP   2m16s
kubernetes-dashboard        ClusterIP   10.109.112.118   <none>        443/TCP    2m16s
root@ip-172-31-22-49:~# kubectl edit   svc kubernetes-dashboard    -n kubernetes-dashboard 
service/kubernetes-dashboard edited
root@ip-172-31-22-49:~# 
root@ip-172-31-22-49:~# kubectl get  svc -n kubernetes-dashboard 
NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)         AGE
dashboard-metrics-scraper   ClusterIP   10.111.75.201    <none>        8000/TCP        3m37s
kubernetes-dashboard        NodePort    10.109.112.118   <none>        443:30904/TCP   3m37s
root@ip-172-31-22-49:~# 


```

### generate token for sa to login into dashboard 

```
kubectl -n kubernetes-dashboard create token  kubernetes-dashboard 
```

### givng RBAC clusterrole to sa of k8s dashboard 

```
root@ip-172-31-22-49:~# kubectl get clusterrole
NAME                                                                   CREATED AT
admin                                                                  2023-04-05T08:06:17Z
calico-kube-controllers                                                2023-04-05T08:34:29Z
calico-node                                                            2023-04-05T08:34:29Z
cluster-admin                                                          2023-04-05T08:06:17Z
edit               
```

### binding it 

```
548  kubectl create clusterrolebinding  web-bind --clusterrole  cluster-admin --serviceaccount=kubernetes-dashboard:kubernetes-dashboard  --dry-run=client -o yaml >bind.yaml 
  549  vim bind.yaml 
  550  kubectl apply -f bind.yaml  
  551  kubectl  get  clusterrolebindings.rbac.authorization.k8s.io 
```

### k8s Internal communication security 

<img src="sec1.png">

### checking default tls certificates 

```
root@ip-172-31-22-49:~# cd  /etc/kubernetes/
root@ip-172-31-22-49:/etc/kubernetes# ls
admin.conf  controller-manager.conf  kubelet.conf  manifests  pki  scheduler.conf
root@ip-172-31-22-49:/etc/kubernetes# 
root@ip-172-31-22-49:/etc/kubernetes# 
root@ip-172-31-22-49:/etc/kubernetes# cd  pki/
root@ip-172-31-22-49:/etc/kubernetes/pki# ls
apiserver-etcd-client.crt     apiserver-kubelet-client.key  ca.crt  front-proxy-ca.crt      front-proxy-client.key
apiserver-etcd-client.key     apiserver.crt                 ca.key  front-proxy-ca.key      sa.key
apiserver-kubelet-client.crt  apiserver.key                 etcd    front-proxy-client.crt  sa.pub
root@ip-172-31-22-49:/etc/kubernetes/pki# 
root@ip-172-31-22-49:/etc/kubernetes/pki# 
root@ip-172-31-22-49:/etc/kubernetes/pki# ls etcd/
ca.crt  ca.key  healthcheck-client.crt  healthcheck-client.key  peer.crt  peer.key  server.crt  server.key
root@ip-172-31-22-49:/etc/kubernetes/pki# 


```

### more info 

```
root@ip-172-31-22-49:/etc/kubernetes# pwd
/etc/kubernetes
root@ip-172-31-22-49:/etc/kubernetes# ls
admin.conf  controller-manager.conf  kubelet.conf  manifests  pki  scheduler.conf
root@ip-172-31-22-49:/etc/kubernetes# cd manifests/
root@ip-172-31-22-49:/etc/kubernetes/manifests# ls
etcd.yaml  kube-apiserver.yaml  kube-controller-manager.yaml  kube-scheduler.yaml
root@ip-172-31-22-49:/etc/kubernetes/manifests# vim kube-apiserver.yaml 
root@ip-172-31-22-49:/etc/kubernetes/manifests# vim etcd.yaml 
root@ip-172-31-22-49:/etc/kubernetes/manifests# 



```

### oVer application traffic flow and SSL with Ingress

<img src="ingress.png">

### Deploy Nginx ingress controller 

```
root@ip-172-31-22-49:~# kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/baremetal/deploy.yaml
namespace/ingress-nginx created
serviceaccount/ingress-nginx created
serviceaccount/ingress-nginx-admission created
role.rbac.authorization.k8s.io/ingress-nginx created
```

### verify it

```

```

### creating clusterIP service for any app exposing with ingress

<img src="svc.png">

### svc , EP in k8s 

```
root@ip-172-31-22-49:~# kubectl  get  deploy 
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
d1     3/3     3            3           6m6s
root@ip-172-31-22-49:~# kubectl  get  po
NAME                  READY   STATUS    RESTARTS   AGE
d1-6cb868647c-5wd2r   1/1     Running   0          6m10s
d1-6cb868647c-8x8mb   1/1     Running   0          6m10s
d1-6cb868647c-zhh9q   1/1     Running   0          6m10s
root@ip-172-31-22-49:~# 
root@ip-172-31-22-49:~# 
root@ip-172-31-22-49:~# kubectl  expose deployment d1  --type ClusterIP --port 80 --name lb1 
service/lb1 exposed
root@ip-172-31-22-49:~# kubectl get svc
NAME   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
lb1    ClusterIP   10.103.121.72   <none>        80/TCP    3s
root@ip-172-31-22-49:~# 
root@ip-172-31-22-49:~# 
root@ip-172-31-22-49:~# kubectl get   ep 
NAME   ENDPOINTS                                              AGE
lb1    192.168.126.78:80,192.168.126.79:80,192.168.2.215:80   20s
root@ip-172-31-22-49:~# 


```

### understanding network flow 

<img src="net.png">

### creating ingress rule 

```
root@ip-172-31-22-49:~# cat  ingress.yaml 
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ashu-app-route-rule
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: test.ashutoshh.in 
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: lb1 
            port:
              number: 80
```

### deploy it 

```
root@ip-172-31-22-49:~# kubectl apply -f ingress.yaml 
ingress.networking.k8s.io/ashu-app-route-rule created
root@ip-172-31-22-49:~# 
root@ip-172-31-22-49:~# 
root@ip-172-31-22-49:~# kubectl  get ingress 
NAME                  CLASS   HOSTS               ADDRESS   PORTS   AGE
ashu-app-route-rule   nginx   test.ashutoshh.in             80      10s
root@ip-172-31-22-49:~# kubectl  get ingress 
root@ip-172-31-22-49:~# kubectl  get ingress 
NAME                  CLASS   HOSTS               ADDRESS        PORTS   AGE
ashu-app-route-rule   nginx   test.ashutoshh.in   172.31.29.26   80      57s



```
###

```
root@ip-172-31-22-49:~# kubectl get  ingress
NAME                  CLASS   HOSTS               ADDRESS        PORTS   AGE
ashu-app-route-rule   nginx   test.ashutoshh.in   172.31.29.26   80      2m33s
root@ip-172-31-22-49:~# kubectl get  svc
NAME   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
lb1    ClusterIP   10.103.121.72   <none>        80/TCP    8m7s
root@ip-172-31-22-49:~# 
root@ip-172-31-22-49:~# kubectl get  ep
NAME   ENDPOINTS                                              AGE
lb1    192.168.126.78:80,192.168.126.79:80,192.168.2.215:80   8m12s
root@ip-172-31-22-49:~# 
root@ip-172-31-22-49:~# 
root@ip-172-31-22-49:~# kubectl get  po -owide
NAME                  READY   STATUS    RESTARTS   AGE   IP               NODE               NOMINATED NODE   READINESS GATES
d1-6cb868647c-5wd2r   1/1     Running   0          14m   192.168.126.78   ip-172-31-25-185   <none>           <none>
d1-6cb868647c-8x8mb   1/1     Running   0          14m   192.168.126.79   ip-172-31-25-185   <none>           <none>
d1-6cb868647c-zhh9q   1/1     Running   0          14m   192.168.2.215    ip-172-31-29-26    <none>           <none>
root@ip-172-31-22-49:~# 

```




