# สร้าง Kubernetes Configuration Files สำหรับ Authentication
Kubernetes Configuration Files หรืออาจจะเรียกสั้น ๆ ว่า kubeconfig ใช้เพื่อให้ Client ระบุ Kubernetes API Server ว่า cluster ไหน IP อะไร ให้บริการ port อะไร ร่วมถึง CA Certicate ที่จะในตอนเชื่อมต่อแบบ TLS, ระบุ credential ในการเชื่อมต่อไปยัง Kubernetes API Server และสุดท้ายก็จะมี context ที่จะจับคู่ระหว่าง credential กับ cluster ว่าจะใช้ credentail ตัวไหน เชื่อมต่อไปยัง cluster ไหน 
## Kubernetes Configuration Files ต้องสร้างกี่ไฟล์
จากหน้าที่ของ Kubernetes Configuration Files ในช่วงแรก ดังนั้นส่วนประกอบใดที่ต้องเชื่อมต่อกับ Kubernetes API Server ก็ต้องสร้าง Kubernetes Configuration Files เตรียมไว้
![Kubernetes and TLS Information](https://github.com/rdamrong/Kubernetes-The-Hard-Way-CentOS/blob/master/images/kube-component.png)


## สร้าง kubelet Kubernetes Configuration File
```
LOADBALANCER_ADDRESS=192.168.254.60
{
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.crt \
    --embed-certs=true \
    --server=https://${LOADBALANCER_ADDRESS}:6443 \
    --kubeconfig=node0.kubeconfig

  kubectl config set-credentials system:node:node0 \
    --client-certificate=node0.crt \
    --client-key=node0.key \
    --embed-certs=true \
    --kubeconfig=node0.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:node:node0 \
    --kubeconfig=node0.kubeconfig

  kubectl config use-context default --kubeconfig=node0.kubeconfig
}

{
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.crt \
    --embed-certs=true \
    --server=https://${LOADBALANCER_ADDRESS}:6443 \
    --kubeconfig=node1.kubeconfig

  kubectl config set-credentials system:node:node1 \
    --client-certificate=node1.crt \
    --client-key=node1.key \
    --embed-certs=true \
    --kubeconfig=node1.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:node:node1 \
    --kubeconfig=node1.kubeconfig

  kubectl config use-context default --kubeconfig=node1.kubeconfig
}
```
## สร้าง kube-proxy Kubernetes Configuration File
```
LOADBALANCER_ADDRESS=192.168.254.60
kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.crt \
    --embed-certs=true \
    --server=https://${LOADBALANCER_ADDRESS}:6443 \
    --kubeconfig=kube-proxy.kubeconfig

  kubectl config set-credentials system:kube-proxy \
    --client-certificate=kube-proxy.crt \
    --client-key=kube-proxy.key \
    --embed-certs=true \
    --kubeconfig=kube-proxy.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:kube-proxy \
    --kubeconfig=kube-proxy.kubeconfig

  kubectl config use-context default --kubeconfig=kube-proxy.kubeconfig
```
## สร้าง kube-controller-manager Kubernetes Configuration File
```
kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.crt \
    --embed-certs=true \
    --server=https://127.0.0.1:6443 \
    --kubeconfig=kube-controller-manager.kubeconfig

  kubectl config set-credentials system:kube-controller-manager \
    --client-certificate=kube-controller-manager.crt \
    --client-key=kube-controller-manager.key \
    --embed-certs=true \
    --kubeconfig=kube-controller-manager.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:kube-controller-manager \
    --kubeconfig=kube-controller-manager.kubeconfig

  kubectl config use-context default --kubeconfig=kube-controller-manager.kubeconfig

```
## สร้าง kube-scheduler Kubernetes Configuration File
```
kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.crt \
    --embed-certs=true \
    --server=https://127.0.0.1:6443 \
    --kubeconfig=kube-scheduler.kubeconfig

  kubectl config set-credentials system:kube-scheduler \
    --client-certificate=kube-scheduler.crt \
    --client-key=kube-scheduler.key \
    --embed-certs=true \
    --kubeconfig=kube-scheduler.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:kube-scheduler \
    --kubeconfig=kube-scheduler.kubeconfig

  kubectl config use-context default --kubeconfig=kube-scheduler.kubeconfig
```
## สร้าง admin Kubernetes Configuration File
```
kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.crt \
    --embed-certs=true \
    --server=https://127.0.0.1:6443 \
    --kubeconfig=admin.kubeconfig

  kubectl config set-credentials admin \
    --client-certificate=admin.crt \
    --client-key=admin.key \
    --embed-certs=true \
    --kubeconfig=admin.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=admin \
    --kubeconfig=admin.kubeconfig

  kubectl config use-context default --kubeconfig=admin.kubeconfig
```
**Next>** [สร้าง Data Encryption Config และ Key](04-generating-data-encryption-key.md)

**<Prev** [แนวคิดในการสร้าง TLS Certicate ใน Kubernetes และ สร้าง Certificate Authority กับ TLS Certificate](02-generating-tls-certificate.md)
