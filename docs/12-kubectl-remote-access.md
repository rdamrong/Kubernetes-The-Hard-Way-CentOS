# สร้าง Kubernetes Configuration File สำหรับ remote access
จากที่ผ่านมาใช้จะส่งค่า kubeconfig file ผ่าน option --kubeconfig กับ kubectl ในเอกสารฉบับนี้จะสร้าง kubeconfig file ให้อยู่ในตำแหน่งที่เป็น default ที่ไม่จำเป็นต้องระบุ -kubeconfig ซึ่งไฟล์นั้นจะถูกเก็บไว้ที่ $HOME/.kube/config ซึ่งสามารถสร้างที่ไหนก็ได้ที่สามารถติดตั้ง kubectl ได้ ไม่จำเป็นต้องสร้างหรือใช้งานจากภายใน Kubernetes Cluster 
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
**Next>** [ติดตั้ง Network สำหรับ Pod](13-pod-network.md)

**<Prev** [Kubernetes Object Authorization](11-kubernetes-object-authorization.md)
