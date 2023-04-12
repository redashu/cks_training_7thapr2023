## Revision 

<img src="rev.png">

### Target for today 

<img src="tg.png">

### Note : even we don't have permmission of kubeconfig file but by using token of default svc account we can send some request from inside pod

```
root@ashupod1:/var/run/secrets/kubernetes.io/serviceaccount# curl https://10.96.0.1 -k 
{
  "kind": "Status",
  "apiVersion": "v1",
  "metadata": {},
  "status": "Failure",
  "message": "forbidden: User \"system:anonymous\" cannot get path \"/\"",
  "reason": "Forbidden",
  "details": {},
  "code": 403
}root@ashupod1:/var/run/secrets/kubernetes.io/serviceaccount# ls
ca.crt	namespace  token
root@ashupod1:/var/run/secrets/kubernetes.io/serviceaccount# curl https://10.96.0.1 -k -H "Authorization: Bearer $(cat token)"
{
  "kind": "Status",
  "apiVersion": "v1",
  "metadata": {},
  "status": "Failure",
  "message": "forbidden: User \"system:serviceaccount:testing:default\" cannot get path \"/\"",
  "reason": "Forbidden",
  "details": {},
  "code": 403
}root@ashupod1:/var/run/secrets/kubernetes.io/serviceaccount# 



```

### pod yaml as per best security practise 

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: ashupod1
  name: ashupod1
spec:
  serviceAccount: access
  automountServiceAccountToken: false
  containers:
  - image: nginx
    name: ashupod1
    ports:
    - containerPort: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

```

