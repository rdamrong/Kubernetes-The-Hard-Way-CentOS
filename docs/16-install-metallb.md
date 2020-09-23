# ติดตั้ง Metallb เมื่อต้องการใช้ Service แบบ LoadฺBalancer
หากต้องการใช้ Service แบบ Loadlbalancer จำเป็นต้องติดตั้ง software เพิ่มเติม ผู้เขียนเลือก Metallb เป็นตัวอย่างในเอกสารชุดนี้
## แก้ไข Kube-Proxy Configuration และ Restart kube-proxy service [all worker node]
```
[root@node0 kube-proxy]# cat /var/lib/kube-proxy/kube-proxy-config.yaml
kind: KubeProxyConfiguration
apiVersion: kubeproxy.config.k8s.io/v1alpha1
clientConnection:
  kubeconfig: "/var/lib/kube-proxy/kubeconfig"
mode: "ipvs"
ipvs:
  staticARP: true
clusterCIDR: "10.0.0.0/16"


systemctl restart kube-proxy
```
## ติดตั้ง Metallb และกำหนดค่า ConfigMap สำหรับ Metallb
```
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/metallb.yaml
# On first install only
kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
[root@kube-master ~]# cat metallb-configmap.yml
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 192.168.254.200-192.168.254.250

kubectl create -f metallb-configmap.yml
```
## ทดสอบการทำงานของ Metallb โดยการสร้าง Service แบบ Loadbalancer
```
[root@kube-master ~]# cat nginx-deployment.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
kubectl apply -f nginx-deployment.yml

[root@kube-master ~]# cat nginx-service.yml
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 80
    name: http

kubectl apply -f nginx-service.yml


[root@kube-master ~]# kubectl get services
NAME    TYPE           CLUSTER-IP      EXTERNAL-IP       PORT(S)        AGE
nginx   LoadBalancer   10.109.67.153   192.168.254.200   80:32199/TCP   27m
```
> จะพบว่า มี Service ขนิดที่เป็น LoadBalancer และมีค่าของ External-IP อยู่
