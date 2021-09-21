# โอนถ่ายไฟล์ที่สร้างไปยัง Virtual Machine
ในขั้นตอนที่จะโอนถ่ายไฟล์ที่ถูกสร้างเตรียมไว้ ไปยัง Virtual Machine ตามหน้าที่ และความจำเป็นต้องใช้งาน
```
for i in 192.168.254.61 192.168.254.62 192.168.254.63; do
    echo "Copy certificates and keys to $i" 
    scp admin.key admin.crt ca.crt ca.key kube-apiserver.key kube-apiserver.crt \
    service-account.key service-account.crt etcd-server.key etcd-server.crt \
    encryption-config.yaml admin.kubeconfig kube-controller-manager.kubeconfig \
    kube-scheduler.kubeconfig root@${i}:~/
done

for i in 192.168.254.64 192.168.254.65; do
    echo "Copy kube-proxy.kubeconfig to $i"
    scp kube-proxy.kubeconfig root@${i}:~/
done

scp ca.crt node0.crt node0.key node0.kubeconfig root@192.168.254.64:~/
scp ca.crt node1.crt node1.key node1.kubeconfig root@192.168.254.65:~/
```
**Next>** [ติดตั้ง etcd](07-install_etcd_cluster.md)

**<Prev** [เคล็ดไม่ลับ เล็ก ๆ น้อย ๆ](05-tip-n-trick.md)
