# ຕິດຕັ້ງ etcd

etcd ເຮັດໜ້າທີ່ເກັບຂໍ້ມູນຕ່າງໆຂອງ cluster ບໍ່ວ່າຈະເປັນຂໍ້ມູນຂອງ cluster ເອງ ແລະ ຂໍ້ມູນສະຖານະໃນຕອນທີ່ Kubernetes ກຳລັງເຮັດວຽກ ໃນເອກະສານສະບັບນີ້ etcd ຈະຖືກຕິດຕັ້ງທີ່ master node ແລະ ກຳນົດຄ່າໃຫ້ etcd ເຮັດວຽກໃນຮູບແບບ High Availability ແລະ ຄຳນຶງເຖິງຄວາມປອດໄພໃນການເຊື່ອມຕໍ່ ຫາກຕ້ອງການຂໍ້ມູນເພີ່ມເຕີມສາມາດເບິ່ງໄດ້ທີ່ [etcd](https://github.com/etcd-io/etcd)


## ກຽມ etcd binaries [all master node]
```
{
ETCD_VER=v3.4.13
DOWNLOAD_URL=https://storage.googleapis.com/etcd

curl -L ${DOWNLOAD_URL}/${ETCD_VER}/etcd-${ETCD_VER}-linux-amd64.tar.gz -o etcd-${ETCD_VER}-linux-amd64.tar.gz
tar xzvf etcd-${ETCD_VER}-linux-amd64.tar.gz
mv etcd-${ETCD_VER}-linux-amd64/etcd* /usr/local/bin
/usr/local/bin/etcd --version
ETCDCTL_API=3 /usr/local/bin/etcdctl version
}
```
## ກຽມ directory ແລະ key pair ທີ່ໃຊ້ໃນການເຮັດວຽກຂອງ etcd [all master node]
```
{
mkdir -p /etc/etcd /var/lib/etcd
 chmod 700 /var/lib/etcd 
 cp ca.crt etcd-server.key etcd-server.crt /etc/etcd/
}
```
## ກຽມຕົວແປສຳລັບສ້າງ `etcd.service` [all master node]
```
{
INTERNAL_IP=$(ip addr show eth0 | grep "inet " | awk '{print $2}' | cut -d / -f 1)
ETCD_NAME=$(hostname -s)
echo $INTERNAL_IP $ETCD_NAME
}
```
> eth0 ເປັນຊື່ network interface card ເຊິ່ງອາດຈະແຕກຕ່າງກັນ
## ສ້າງ `etcd.service` ສຳລັບ systemd [all master node]
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
## ເລີ່ມການເຮັດວຽກຂອງ etcd [all master node]
```
{
 systemctl daemon-reload
 systemctl enable --now etcd
}
```


## ທົດສອບການເຮັດວຽກຂອງ etcd [all master node]
```
ETCDCTL_API=3 etcdctl member list \
 --endpoints=https://127.0.0.1:2379 \
 --cacert=/etc/etcd/ca.crt \
 --cert=/etc/etcd/etcd-server.crt \
 --key=/etc/etcd/etcd-server.key
 ```
 > ຜົນການທົດສອບ
 ```
41cf2dc7f859bfed, started, master2, https://192.168.254.63:2380, https://192.168.254.63:2379, false
8283bd82ad10dcb8, started, master1, https://192.168.254.62:2380, https://192.168.254.62:2379, false
fcae0ac3856fb516, started, master0, https://192.168.254.61:2380, https://192.168.254.61:2379, false
```
**Next>** [ຕິດຕັ້ງ Kubernetes Control Plane](08-install_kubernetes_control_plane.md)

**<Prev** [ການຍ້າຍໄຟລທີ່ສ້າງໄປຍັງ Virtual Machine](06-transfer-file.md)
