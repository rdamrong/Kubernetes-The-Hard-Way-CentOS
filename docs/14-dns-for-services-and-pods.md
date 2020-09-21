# ติดตั้ง DNS สำหรับ Services และ Pods
ทุก services ที่ถูกสร้างภายใน Cluster จะได้มีชื่อนั้นถูกกำหนดใน DNS เช่นกัน ทำให้สามาถใช้ชื่อในการอ้างถึง service ได้โดยมีรูปแบบ `***servicename***.***namespace***.svc.cluster.local` ในเอกสารชุดนี้จะใช้ [CoreDNS](https://coredns.io/) ทำหน้าที่นี้

## ติดตั้ง CoreDNS
```
dnf install -y git jq
git clone https://github.com/coredns/deployment.git
cd deployment/
cd kubernetes/
./deploy.sh -i 10.96.0.10 | kubectl apply -f -
```
### ตรวจสอบการติดตั้งของ CoreDNS
```
kubectl get pods -n kube-system
```
> ผลการทดสอบ
```
```
### ทดสอบการทำงานของ CoreDNS
```
kubectl run busybox --image=busybox:1.28  -- nslookup kubernetes
```
> ผลการทดสอบ
```
```
