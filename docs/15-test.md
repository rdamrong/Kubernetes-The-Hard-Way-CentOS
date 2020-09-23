# ทดสอบการทำงาน
## ทดสอบ Pod Network
```
[root@master0 ~]# kubectl get pods -o wide
NAME      READY   STATUS    RESTARTS   AGE   IP          NODE    NOMINATED NODE   READINESS GATES
dnstest   1/1     Running   0          9m    10.32.0.2   node1   <none>           <none>


[root@master0 ~]# kubectl run pingtest -it --image centos -- /bin/bash
If you don't see a command prompt, try pressing enter.

[root@pingtest /]# ping -c 3 10.32.0.2
PING 10.32.0.2 (10.32.0.2) 56(84) bytes of data.
64 bytes from 10.32.0.2: icmp_seq=1 ttl=64 time=1.49 ms
64 bytes from 10.32.0.2: icmp_seq=2 ttl=64 time=0.666 ms
64 bytes from 10.32.0.2: icmp_seq=3 ttl=64 time=0.699 ms

--- 10.32.0.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 17ms
rtt min/avg/max/mdev = 0.666/0.952/1.493/0.383 ms


[root@pingtest /]# exit
exit
Session ended, resume using 'kubectl attach pingtest -c pingtest -i -t' command when the pod is running
```
## ทดสอบสร้าง Deployment
```
[root@master0 ~]# echo "
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dp1
spec:
  replicas: 5
  selector:
    matchLabels:
       run: dp-hello
  template:
    metadata:
      name: my-app
      labels:
        run: dp-hello
    spec:
      containers:
        - name: my-hello
          image: nginxdemos/nginx-hello:plain-text
" | kubectl apply -f -


[root@master0 ~]# kubectl get pods
NAME                   READY   STATUS    RESTARTS   AGE
dnstest                1/1     Running   0          17m
dp1-6fc549dc58-4l686   1/1     Running   0          9s
dp1-6fc549dc58-8pj9v   1/1     Running   0          9s
dp1-6fc549dc58-rd7hp   1/1     Running   0          9s
dp1-6fc549dc58-trcf8   1/1     Running   0          9s
dp1-6fc549dc58-vgb5d   1/1     Running   0          9s
pingtest               1/1     Running   1          8m29s
```
## ทดสอบสร้าง Service แบบ NodePort
```
[root@master0 ~]# echo "
apiVersion: v1
kind: Service
metadata:
  name: svc-np
spec:
  type: NodePort
  selector:
    run: dp-hello
  ports:
    - port: 9090
      protocol: TCP
      targetPort: 8080
      nodePort: 31111
" |  kubectl apply -f -


[root@master0 ~]# kubectl get services
NAME         TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP   10.96.0.1     <none>        443/TCP          34m
svc-np       NodePort    10.96.0.178   <none>        9090:31111/TCP   9s


[root@master0 ~]# curl http://node0.example.com:31111
Server address: 10.44.0.3:8080
Server name: dp1-6fc549dc58-rwmxr
Date: 23/Sep/2020:15:25:40 +0000
URI: /
Request ID: a819dfc9ec38a94e5a8d4bcea85a0f07


[root@master0 ~]# curl http://node1.example.com:31111
Server address: 10.44.0.4:8080
Server name: dp1-6fc549dc58-fngm2
Date: 23/Sep/2020:15:25:44 +0000
URI: /
Request ID: 8ad1b8407f1b7c2759830b04547b5b49


[root@master0 ~]# kubectl exec -it pingtest -- /bin/bash


[root@pingtest /]# curl http://svc-np:9090
Server address: 10.32.0.5:8080
Server name: dp1-6fc549dc58-zgrm5
Date: 23/Sep/2020:15:27:01 +0000
URI: /
Request ID: 2afe05ccb6eb274e4a40f76394a81a39


[root@pingtest /]# curl http://svc-np.default.svc.cluster.local:9090
Server address: 10.44.0.4:8080
Server name: dp1-6fc549dc58-fngm2
Date: 23/Sep/2020:15:27:12 +0000
URI: /
Request ID: 01d1a7c51d4e9a537fa69320e2075c1e
```
d
