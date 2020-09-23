# ติดตั้ง Kubernetes Control Plane

kube-apiserver, kube-controller-manager, kube-scheduler เป็นองค์ประกอบสำคัญของการทำงานของ master node และติดตั้ง kubectl ที่เป็น Kubenetes Client ที่จะเชื่อมต่อกับ Kubenetes Cluster ในเบื้องต้นเพื่อสร้างส่วนที่จำเป็นภายใน Kubenetes Cluster 
## เตรียม Kubenetes Controller binaries [all master node]
```
{
DL=https://dl.k8s.io/v1.19.0/kubernetes-server-linux-amd64.tar.gz
dnf install -y wget
wget -q --show-progress ${DL}

tar xvfz kubernetes-server-linux-amd64.tar.gz
cd kubernetes/server/bin/
mv kube-apiserver kube-controller-manager kube-scheduler kubectl /usr/local/bin/
cd
}
```
## เตรียม directory, key pair และข้อมูลที่ใช้ในการทำงานของ Kubernetes Controller [all master node]
```
mkdir -p /etc/kubernetes/config
mkdir -p /var/lib/kubernetes/
cp ca.crt ca.key kube-apiserver.crt kube-apiserver.key \
   service-account.key service-account.crt \
   etcd-server.key etcd-server.crt \
   encryption-config.yaml /var/lib/kubernetes/
cp kube-controller-manager.kubeconfig /var/lib/kubernetes/
cp kube-scheduler.kubeconfig /var/lib/kubernetes/
INTERNAL_IP=$(ip addr show eth0| grep "inet " | awk '{print $2}' | cut -d / -f 1)
echo $INTERNAL_IP
```
> eth0 เป็นชื่อ network interface card ซึ่งอาจจะต่างกันไป
## สร้าง `kube-apiserver.service` สำหรับ systemd [all master node]
```
cat <<EOF | sudo tee /etc/systemd/system/kube-apiserver.service
[Unit]
Description=Kubernetes API Server
Documentation=https://github.com/kubernetes/kubernetes

[Service]
ExecStart=/usr/local/bin/kube-apiserver \\
 --advertise-address=${INTERNAL_IP} \\
 --allow-privileged=true \\
 --apiserver-count=3 \\
 --audit-log-maxage=30 \\
 --audit-log-maxbackup=3 \\
 --audit-log-maxsize=100 \\
 --audit-log-path=/var/log/audit.log \\
 --authorization-mode=Node,RBAC \\
 --bind-address=0.0.0.0 \\
 --client-ca-file=/var/lib/kubernetes/ca.crt \\
 --enable-admission-plugins=NamespaceLifecycle,NodeRestriction,LimitRanger,ServiceAccount,DefaultStorageClass,ResourceQuota \\
 --enable-bootstrap-token-auth=true \\
 --etcd-cafile=/var/lib/kubernetes/ca.crt \\
 --etcd-certfile=/var/lib/kubernetes/etcd-server.crt \\
 --etcd-keyfile=/var/lib/kubernetes/etcd-server.key \\
 --etcd-servers=https://192.168.254.61:2379,https://192.168.254.62:2379,https://192.168.254.63:2379 \\
 --event-ttl=1h \\
 --encryption-provider-config=/var/lib/kubernetes/encryption-config.yaml \\
 --kubelet-certificate-authority=/var/lib/kubernetes/ca.crt \\
 --kubelet-client-certificate=/var/lib/kubernetes/kube-apiserver.crt \\
 --kubelet-client-key=/var/lib/kubernetes/kube-apiserver.key \\
 --kubelet-https=true \\
 --runtime-config='api/all=true' \\
 --service-account-key-file=/var/lib/kubernetes/service-account.crt \\
 --service-cluster-ip-range=10.96.0.0/24 \\
 --service-node-port-range=30000-32767 \\
 --tls-cert-file=/var/lib/kubernetes/kube-apiserver.crt \\
 --tls-private-key-file=/var/lib/kubernetes/kube-apiserver.key \\
 --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```
## สร้าง `kube-controller-manager.service` สำหรับ systemd [all master node]
```
cat <<EOF | sudo tee /etc/systemd/system/kube-controller-manager.service
[Unit]
Description=Kubernetes Controller Manager
Documentation=https://github.com/kubernetes/kubernetes

[Service]
ExecStart=/usr/local/bin/kube-controller-manager \\
 --address=0.0.0.0 \\
 --cluster-cidr=192.168.254.0/24 \\
 --cluster-name=kubernetes \\
 --cluster-signing-cert-file=/var/lib/kubernetes/ca.crt \\
 --cluster-signing-key-file=/var/lib/kubernetes/ca.key \\
 --kubeconfig=/var/lib/kubernetes/kube-controller-manager.kubeconfig \\
 --leader-elect=true \\
 --root-ca-file=/var/lib/kubernetes/ca.crt \\
 --service-account-private-key-file=/var/lib/kubernetes/service-account.key \\
 --service-cluster-ip-range=10.96.0.0/24 \\
 --use-service-account-credentials=true \\
 --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```
## สร้าง `kube-scheduler.service` สำหรับ systemd [all master node]
```
cat <<EOF | sudo tee /etc/kubernetes/config/kube-scheduler.yaml
apiVersion: kubescheduler.config.k8s.io/v1beta1
kind: KubeSchedulerConfiguration
clientConnection:
  kubeconfig: "/var/lib/kubernetes/kube-scheduler.kubeconfig"
leaderElection:
  leaderElect: true
EOF

cat <<EOF | sudo tee /etc/systemd/system/kube-scheduler.service
[Unit]
Description=Kubernetes Scheduler
Documentation=https://github.com/kubernetes/kubernetes

[Service]
ExecStart=/usr/local/bin/kube-scheduler \\
  --config=/etc/kubernetes/config/kube-scheduler.yaml \\
  --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```
## เริ่มการทำงานของ kube-apiserver, kube-controller-manager และ kube-scheduler [all master node]
```
systemctl daemon-reload
systemctl enable --now   kube-apiserver kube-controller-manager kube-scheduler
```
## ทดสอบและผลทดสอบของ kube-apiserver [all master node]
```
kubectl get --raw='/readyz?verbose' --kubeconfig=admin.kubeconfig
```
> ผลการทดสอบ
```
[+]ping ok
[+]log ok
[+]etcd ok
[+]informer-sync ok
[+]poststarthook/start-kube-apiserver-admission-initializer ok
[+]poststarthook/generic-apiserver-start-informers ok
[+]poststarthook/start-apiextensions-informers ok
[+]poststarthook/start-apiextensions-controllers ok
[+]poststarthook/crd-informer-synced ok
[+]poststarthook/bootstrap-controller ok
[+]poststarthook/rbac/bootstrap-roles ok
[+]poststarthook/scheduling/bootstrap-system-priority-classes ok
[+]poststarthook/priority-and-fairness-config-producer ok
[+]poststarthook/start-cluster-authentication-info-controller ok
[+]poststarthook/start-kube-aggregator-informers ok
[+]poststarthook/apiservice-registration-controller ok
[+]poststarthook/apiservice-status-available-controller ok
[+]poststarthook/kube-apiserver-autoregistration ok
[+]autoregister-completion ok
[+]poststarthook/apiservice-openapi-controller ok
[+]shutdown ok
healthz check passed
```
