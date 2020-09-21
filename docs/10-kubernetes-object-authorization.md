# Kubernetes Object Authorization
ภายใน Kubernetes Cluster เองก็มีการควบคุมการเข้าถึงในแต่ละส่วนด้วยแนวคิด RBAC (Role-Based Access Control) ดังนั้นในการใช้งานจำเป็นต้องกำหนดให้ object ตัวไหน สามารถใช้งาน object ตัวไหนได้บ้าง ในเอกสารนี้จะกำหนดให้ Kube-Apiserver สามารถเข้าถึงข้อมูลบางส่วนของ Kubelet ในบทบาทของ system:kubelet-api-admin
## กำหนดให้ผู้ใช้ชื่อ kubernetes มีสิทธิ์ในบทบาท system:kubelet-api-admin [master0]
```
{
cat <<EOF | kubectl apply --kubeconfig admin.kubeconfig -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: apiserver-kubelet-api-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:kubelet-api-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: kubernetes
EOF
}
```
