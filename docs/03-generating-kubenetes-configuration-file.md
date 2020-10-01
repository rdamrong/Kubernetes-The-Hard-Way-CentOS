# ສ້າງ Kubernetes Configuration Files ສໍາລັບ Authentication
Kubernetes Configuration Files ຫຼືອາດຈະເອີ້ນສັ້ນໆວ່າ kubeconfig ໃຊ້ເພື່ອໃຫ້ Client ລະບຸ Kubernetes API Server ວ່າ cluster ໃດ IP ຫຍັງ ໃຫ້ບໍລິການ port ໃດ ລວມເຖິງ CA Certicate ທີ່ຈະໃຊ້ໃນຕອນເຊື່ອມຕໍ່ແບບ TLS, ລະບຸ credential ໃນການເຊື່ອມຕໍ່ກັບ Kubernetes API Server ແລະສຸດທ້າຍ context ທີ່ຈະຈັບຄູ່ລະຫວ່າງ credential ກັບ cluster ວ່າຈະໃຊ້ credential ຕົວໃດ ເຊື່ອມຕົວໄປຍັງ cluster ໃດ.
## Kubernetes Configuration Files ຕ້ອງສ້າງຈັກໄຟລ໌
ຈາກໜ້າທີ່ຂອງ Kubernetes Configuration Files ໃນຊ່ວງທໍາອິດ ດັ່ງນັ້ນສ່ວນປະກອບໃດທີ່ຕ້ອງເຊື່ອມຕໍ່ກັບ Kubernetes API Server ກໍຕ້ອງສ້າງ Kubernetes Configuration Files ກຽມໄວ້
![Kubernetes and TLS Information](https://github.com/rdamrong/Kubernetes-The-Hard-Way-CentOS/blob/master/images/kube-component.png)


## ສ້າງ kubelet Kubernetes Configuration File
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
## ສ້າງ kube-proxy Kubernetes Configuration File
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
## ສ້າງ kube-controller-manager Kubernetes Configuration File
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
## ສ້າງ admin Kubernetes Configuration File
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
**Next>** [ສ້າງ Data Encryption Config ແລະ Key](04-generating-data-encryption-key.md)

**<Prev** [ແນວຄີດໃນການສ້າງ TLS Certicate ໃນ Kubernetes ແລະ ສ້າງ Certificate Authority ກັບ TLS Certificate](02-generating-tls-certificate.md)
