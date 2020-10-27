# ຕິດຕັ້ງ DNS ສຳລັບ Services ແລະ Pods
ທຸກ services ທີ່ຖືກສ້າງພາຍໃນ Cluster ຈະໄດ້ມີນັ້ນຖືກກຳນົດໃນ DNS ເຊັ່ນກັນ ເຮັດໃຫ້ສາມາດໃຊ້ຊື່ໃນການອ້າງເຖິງ service ໄດ້ໂດຍມີຮູບແບບ `servicename.namespace.svc.cluster.local` ໃນເອກະສານຊຸດນີ້ຈະໃຊ້ [CoreDNS](https://coredns.io/) ເຮັດໜ້າທີ່ນີ້

## ຕິດຕັ້ງ CoreDNS [master0]
```
dnf install -y git jq
git clone https://github.com/coredns/deployment.git
cd deployment/kubernetes/
./deploy.sh -i 10.96.0.10 | kubectl apply -f -
```
### ກວດສອບການຕິດຕັ້ງຂອງ CoreDNS [master0]
```
kubectl get pods -n kube-system
```
> ຜົນການທົດສອບ ຈະເຫັນ pod ຊື່ວ່າ coredns-xxxxxxxxxx-xxxxx ສະຖານະ Running ຢູ່
```
NAME                       READY   STATUS    RESTARTS   AGE
coredns-7bf4bd64bd-zgtnf   1/1     Running   0          14s
weave-net-c8wgm            2/2     Running   0          3m39s
weave-net-lxwpn            2/2     Running   0          3m39s
```
> ຈະເຫັນວ່າມີ pod ທີ່ຊື່ວ່າ coredns-xxxxxxxxxx-xxxxx ສະຖານະ Running ຢູ່
### ທົດສອບການເຮັດວຽກຂອງ CoreDNS [master0]
```
]#  kubectl run dnstest --image centos -- sleep 36000
pod/dnstest created
]# kubectl get pods
NAME      READY   STATUS    RESTARTS   AGE
dnstest   1/1     Running   0          50s
]# kubectl exec -it dnstest -- /bin/bash
[root@dnstest /]# yum install -y bind-utils
[root@dnstest /]# dig kubernetes.default.svc.cluster.local

; <<>> DiG 9.11.13-RedHat-9.11.13-6.el8_2.1 <<>> kubernetes.default.svc.cluster.local
;; global options: +cmd
;; Got answer:
;; WARNING: .local is reserved for Multicast DNS
;; You are currently testing what happens when an mDNS query is leaked to DNS
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 8809
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: 1f618ef8f0a39ff7 (echoed)
;; QUESTION SECTION:
;kubernetes.default.svc.cluster.local. IN A

;; ANSWER SECTION:
kubernetes.default.svc.cluster.local. 5 IN A    10.96.0.1

;; Query time: 0 msec
;; SERVER: 10.96.0.10#53(10.96.0.10)
;; WHEN: Wed Sep 23 15:04:06 UTC 2020
;; MSG SIZE  rcvd: 129

```
**Next>** [ทดสอบการทำงาน](15-test.md)

**<Prev** [ติดตั้ง Network สำหรับ Pod](13-pod-network.md)
