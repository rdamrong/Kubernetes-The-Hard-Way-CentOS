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
kubectl run busybox --image=busybox:1.28  -- sleep 20000
kubectl exec busybox -- nslookup kubernetes
kubectl delete pod busybox
```
> ผลการทดสอบ
```
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

Name:      kubernetes
Address 1: 10.96.0.1 kubernetes.default.svc.cluster.local
```
**Next>**

**<Prev** [ติดตั้ง Network สำหรับ Pod](13-pod-network.md)
