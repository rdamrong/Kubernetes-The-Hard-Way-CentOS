# ติดตั้ง DNS สำหรับ Services และ Pods
ทุก services ที่ถูกสร้างภายใน Cluster จะได้มีชื่อนั้นถูกกำหนดใน DNS เช่นกัน ทำให้สามาถใช้ชื่อในการอ้างถึง service ได้โดยมีรูปแบบ `servicename.namespace.svc.cluster.local` ในเอกสารชุดนี้จะใช้ [CoreDNS](https://coredns.io/) ทำหน้าที่นี้

## ติดตั้ง CoreDNS [master0]
```
dnf install -y git jq
git clone https://github.com/coredns/deployment.git
cd deployment/kubernetes/
./deploy.sh -i 10.96.0.10 | kubectl apply -f -
```
### ตรวจสอบการติดตั้งของ CoreDNS [master0]
```
kubectl get pods -n kube-system
```
> ผลการทดสอบ
```
NAME                       READY   STATUS    RESTARTS   AGE
coredns-7bf4bd64bd-zgtnf   1/1     Running   0          14s
weave-net-c8wgm            2/2     Running   0          3m39s
weave-net-lxwpn            2/2     Running   0          3m39s
```
> จะพบว่ามี pod ที่ชื่อว่า coredns-xxxxxxxxxx-xxxxx สถานะ Running อยู่
### ทดสอบการทำงานของ CoreDNS [master0]
```
]#  **kubectl run dnstest --image centos -- sleep 36000**
pod/dnstest created
]# **kubectl get pods**
NAME      READY   STATUS    RESTARTS   AGE
dnstest   1/1     Running   0          50s
]# **kubectl exec -it dnstest -- /bin/bash**
[root@dnstest /]# **yum install -y bind-utils**
[root@dnstest /]# **dig kubernetes.default.svc.cluster.local**

; <<>> DiG 9.11.13-RedHat-9.11.13-6.el8_2.1 <<>> kubernetes.default.svc.cluster.local
;; global options: +cmd
;; Got answer:
;; WARNING: .local is reserved for Multicast DNS
;; You are currently testing what happens when an mDNS query is leaked to DNS
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 8809
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: 1f618ef8f0a39ff7 (echoed)
;; QUESTION SECTION:
;kubernetes.default.svc.cluster.local. IN A

;; ANSWER SECTION:
kubernetes.default.svc.cluster.local. 5 IN A    10.96.0.1

;; Query time: 0 msec
;; SERVER: 10.96.0.10#53(10.96.0.10)
;; WHEN: Wed Sep 23 15:04:06 UTC 2020
;; MSG SIZE  rcvd: 129

```
**Next>**

**<Prev** [ติดตั้ง Network สำหรับ Pod](13-pod-network.md)
