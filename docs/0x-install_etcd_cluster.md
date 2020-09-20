# ติดตั้ง etcd

etcd กจะถูกติดตั้งที่ master node ในเอกสารชุดนี้จะกำหนดค่าให้ etcd ทำงานในรูปแบบ High Availability และคำนึงถึงความปลอดภัยในการเชื่อมต่อ หากต้องการข้อมูลเพิ่มเติมได้ที่ [etcd](https://github.com/etcd-io/etcd)


## เตรียม etcd binaries
```
{
ETCD_VER=v3.4.13
GOOGLE_URL=https://storage.googleapis.com/etcd
DOWNLOAD_URL=${GOOGLE_URL}
rm -f /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz
rm -rf /tmp/etcd-download-test && mkdir -p /tmp/etcd-download-test
curl -L ${DOWNLOAD_URL}/${ETCD_VER}/etcd-${ETCD_VER}-linux-amd64.tar.gz -o /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz
tar xzvf /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz -C /tmp/etcd-download-test --strip-components=1
rm -f /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz
/tmp/etcd-download-test/etcd --version
ETCDCTL_API=3 /tmp/etcd-download-test/etcdctl version
mv /tmp/etcd-download-test/etcd* /usr/local/bin
}
```
## เตรียม directory และ key pair ที่ใช้ในการทำงานของ etcd
```
{
 sudo mkdir -p /etc/etcd /var/lib/etcd
 chmod 700 /var/lib/etcd 
 cp ca.crt etcd-server.key etcd-server.crt /etc/etcd/
}
```
## เตรียมตัวแปรสำหรับสร้าง `etcd.service`
```
{
INTERNAL_IP=$(ip addr show eth0 | grep "inet " | awk '{print $2}' | cut -d / -f 1)
ETCD_NAME=$(hostname -s)
echo $INTERNAL_IP $ETCD_NAME
}
```
*# eth0 เป็นชื่อ network interface card ซึ่งอาจจะต่างกันไป* 
## สร้าง `etcd.service` สำหรับ systemd
```
{
cat <<EOF | sudo tee /etc/systemd/system/etcd.service
[Unit]
Description=etcd
Documentation=https://github.com/coreos

[Service]
ExecStart=/usr/local/bin/etcd \\
 --name ${ETCD_NAME} \\
 --cert-file=/etc/etcd/etcd-server.crt \\
 --key-file=/etc/etcd/etcd-server.key \\
 --peer-cert-file=/etc/etcd/etcd-server.crt \\
 --peer-key-file=/etc/etcd/etcd-server.key \\
 --trusted-ca-file=/etc/etcd/ca.crt \\
 --peer-trusted-ca-file=/etc/etcd/ca.crt \\
 --peer-client-cert-auth \\
 --client-cert-auth \\
 --initial-advertise-peer-urls https://${INTERNAL_IP}:2380 \\
 --listen-peer-urls https://${INTERNAL_IP}:2380 \\
 --listen-client-urls https://${INTERNAL_IP}:2379,https://127.0.0.1:2379 \\
 --advertise-client-urls https://${INTERNAL_IP}:2379 \\
 --initial-cluster-token etcd-cluster-0 \\
 --initial-cluster master0=https://192.168.254.61:2380,master1=https://192.168.254.62:2380,master2=https://192.168.254.63:2380 \\
 --initial-cluster-state new \\
 --data-dir=/var/lib/etcd
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
}
```
## เริ่ม start etcd และทดสอบ
```
{
 systemctl daemon-reload
 systemctl enable --now etcd

ETCDCTL_API=3 etcdctl member list \
 --endpoints=https://127.0.0.1:2379 \
 --cacert=/etc/etcd/ca.crt \
 --cert=/etc/etcd/etcd-server.crt \
 --key=/etc/etcd/etcd-server.key
}
```
## ผลการทดสอบ
