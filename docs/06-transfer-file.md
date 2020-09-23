# โอนถ่ายไฟล์ที่สร้างไปยัง Virtual Machine

```
for i in 192.168.254.61 192.168.254.62 192.168.254.63; do \
    echo $i; 
    scp admin.key admin.crt ca.crt ca.key kube-apiserver.key kube-apiserver.crt \
    service-account.key service-account.crt etcd-server.key etcd-server.crt \
    encryption-config.yaml admin.kubeconfig kube-controller-manager.kubeconfig \
    kube-scheduler.kubeconfig root@${i}:.; \
done

for i in 192.168.254.64 192.168.254.65; do \
    scp kube-proxy.kubeconfig root@${i}:~/; \
done


scp ca.crt node0.crt node0.key node0.kubeconfig root@192.168.254.64:.
scp ca.crt node1.crt node1.key node1.kubeconfig root@192.168.254.65:.
```
