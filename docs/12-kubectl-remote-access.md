# ສ້າງ Kubernetes Configuration File ສຳລັບ remote access
ຈາກທີ່ຜ່ານມາໃຊ້ຈະສົ່ງຄ່າ kubeconfig file ຜ່ານ option --kubeconfig ກັບ kubectl ໃນເອກະສານສະບັບນີ້ຈະສ້າງ kubeconfig file ໃຫ້ຢູ່ໃນຕຳແໜ່ງທີ່ເປັນ default ທີ່ບໍ່ຈຳເປັນຕ້ອງລະບຸ -kubeconfig ເຊິ່ງໄຟຣ໌ນັ້ນຈະຖືກເກັບໄວ້ທີ່ $HOME/.kube/config ເຊິ່ງສາມາດສ້າງຢູ່ໃສກໍ່ໄດ້ທີ່ສາມາດຕິດຕັ້ງ kubectl ໄດ້ ບໍ່ຈຳເປັນຕ້ອງສ້າງຫຼືໃຊ້ວຽກຈາກພາຍໃນ Kubernetes Cluster 
```
{
  KUBERNETES_LB_ADDRESS=192.168.254.60

  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.crt \
    --embed-certs=true \
    --server=https://${KUBERNETES_LB_ADDRESS}:6443

  kubectl config set-credentials admin \
    --client-certificate=admin.crt \
    --client-key=admin.key

  kubectl config set-context kubernetes-the-hard-way \
    --cluster=kubernetes-the-hard-way \
    --user=admin

  kubectl config use-context kubernetes-the-hard-way
}
```
**Next>** [ຕິດຕັ້ງ Network ສຳລັບ Pod](13-pod-network.md)

**<Prev** [Kubernetes Object Authorization](11-kubernetes-object-authorization.md)
