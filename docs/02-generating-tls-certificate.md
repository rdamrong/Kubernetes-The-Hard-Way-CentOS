# แนวคิดในการสร้าง TLS Certicate ใน Kubernetes และ สร้าง Certificate Authority กับ TLS Certificate 
ในการสื่อสารกับ Kubernetes ทั้งภายในและภายนอก Cluster เรื่องความปลอดภัยในการสื่อสารเป็นเรื่องที่ได้รับการออกแบบให้มีความปลอดภัย และ [TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security) ก็เป็นวิธีการที่ถูกเลือกใช้ในการจัดการเรื่องนี้ ส่วนเรื่องของการ Authentication ของ client ในการขอเชื่อมต่อก็จะใช้วิธีการที่เรียกว่า [Mutual Authentication แบบ Certificated based](https://en.wikipedia.org/wiki/Mutual_authentication) ดังนั้นจึงจำเป็นต้องสร้าง key pair จำนวนมากในการติดตั้ง Kubernetes
## ต้องสร้าง Key Pair ที่ไหนบ้าง 
ในการใช้งาน TLS ส่วนของการ Authenitation ใน TLS ใช้ [Public-Key Cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography) ในส่วนนี้จึงสร้าง key pair โดยหลักคิดก็คือว่า component ตัวไหนทำหน้าที่เป็น Server ในการให้บริการก็ต้องสร้าง key pair ให้ และ component ไหนทำหน้าที่เป็น client ในการขอใช้บริการก็ต้องสร้าง key pair ให้
![Kubernetes and TLS Information](https://github.com/rdamrong/Kubernetes-The-Hard-Way-CentOS/blob/master/images/kube-component.png)
- วงกลมสีฟ้าที่มีอักษร S แสดงในองค์ประกอบนั้นทำหน้าที่เป็น Server ที่ทำหน้าที่ให้บริการ service ในหน้าที่ของตัวเอง
- วงกลมสีฟ้าที่มีอักษร C แสดงในองค์ประกอบนั้นทำหน้าที่เป็น Client ต้องขอใช้บริการใน service ที่ต้องการ
> ในเอกสาร version แรกจะเป็นไปตามแนวทางที่ผู้เขียนอ้างอิงถึงจะมีจุดสังเกตุอยู่ 2 จุด
> - client key pair จาก kube-apiserver ไปยัง etcd จะใช้ key pair ของ etcd ในการเชื่อมต่อ
> - client key pair จาก kube-apiserver ไปยัง kubelet จะใช้ key pair ของ kube-apiserver ในการเชื่อมต่อ
> - client key pair จาก kubelet ไปยัง kube-apiserver จะใช้ key pair ของ kubelet ในการเชื่อมต่อ

## สร้าง CA (Certficate Authority) [Admin Workstation]
ประเด็นหนึ่งในการทำ Mutual Authentication แบบ Certificated based คือ Ceriticate ในฝั่ง Client จะต้องมี Digital Signature ที่ถูก sign จาก CA เดียวกับฝั่ง Server ดังนั้นการสร้าง CA key pair จึงเป็นขั้นตอนแรก และเครื่องมือที่จะช่วยในการสร้าง key pair ก็คือ `cfssl`
```
{

cat > ca-config.json <<EOF
{
  "signing": {
    "default": {
      "expiry": "8760h"
    },
    "profiles": {
      "kubernetes": {
        "usages": ["signing", "key encipherment", "server auth", "client auth"],
        "expiry": "8760h"
      }
    }
  }
}
EOF

cat > ca-csr.json <<EOF
{
  "CN": "Kubernetes",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "TH",
      "O": "Kubernetes",
      "OU": "CA",
      "ST": "Bangkok"
    }
  ]
}
EOF

cfssl gencert -initca ca-csr.json | cfssljson -bare ca

cp ca-key.pem ca.key
cp ca.pem ca.crt

}

```
> เนื่องจากผู้เขียน มีความเห็นส่วนตัวเรื่องการตั้งชื่อไฟล์ของ Key และ Certificate ว่าการตั้งไฟล์ที่ลงท้ายด้วย .key และ .crt ทำให้ผู้เขียนจัดการไฟล์ได้สะดวกกว่า
## สร้าง Client และ Server Key Pair
ในส่วนนี้จะเป็นการ Key Pair สำหรับส่วนประกอบต่าง ๆ ที่จำเป็นในการติดตั้ง Kubernetes
### สร้าง Admin Client Key Pair
```
{
cat > admin-csr.json <<EOF
{
  "CN": "admin",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "TH",
      "O": "system:masters",
      "OU": "Kubernetes The Hard Way CentOS",
      "ST": "Bangkok"
    }
  ]
}
EOF

cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -profile=kubernetes \
  admin-csr.json | cfssljson -bare admin

mv admin-key.pem admin.key
mv admin.pem admin.crt

}
```
### สร้าง Kubelet Client Key Pair
ใน Kubernetes มีการกำหนดเรื่อง [Node Authorization](https://kubernetes.io/docs/reference/access-authn-authz/node/) เพื่อให้จัดการเรื่องกำหนดสิทธิ์ในการเรียก API ในไปยัง Kubelet เรียกว่า Node Authorizer มีการกำหนด Group ที่ต้องเป็น system:nodes และ Username จะเป็น system:node:<nodeName> ซึ่งจะกำหนดที่ CommonName และ Organization ใน Certificate Attributes
```
{
declare -A NODE_IP
NODE_IP[node0]="192.168.254.64"
NODE_IP[node1]="192.168.254.65"


for instance in node0 node1; do
cat > ${instance}-csr.json <<EOF
{
  "CN": "system:node:${instance}",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "TH",
      "O": "system:nodes",
      "OU": "Kubernetes The Hard Way CentOS",
      "ST": "Bangkok"
    }
  ]
}
EOF


INTERNAL_IP=${NODE_IP[${instance}]}
echo "\n\nGenerating Key and Cert for "${INTERNAL_IP}" :: "${instance}

cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -hostname=${instance},${INTERNAL_IP} \
  -profile=kubernetes \
  ${instance}-csr.json | cfssljson -bare ${instance}

mv ${instance}-key.pem ${instance}.key
mv ${instance}.pem ${instance}.crt
done
}
```
> ในส่วนของการสร้าง Kubelet Client Certificate ทดสอบบน zsh ใน macOS แล้วใช้งานได้ แต่ยังไม่ได้สอบใน bash 
### สร้าง Controller Manager Client Certificate
```
{

cat > kube-controller-manager-csr.json <<EOF
{
  "CN": "system:kube-controller-manager",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "TH",
      "O": "system:kube-controller-manager",
      "OU": "Kubernetes The Hard Way CentOS",
      "ST": "Bangkok"
    }
  ]
}
EOF

cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -profile=kubernetes \
  kube-controller-manager-csr.json | cfssljson -bare kube-controller-manager

mv kube-controller-manager-key.pem kube-controller-manager.key
mv kube-controller-manager.pem kube-controller-manager.crt
}

```
### สร้าง Kube Proxy Client Key Pair
```
{

cat > kube-proxy-csr.json <<EOF
{
  "CN": "system:kube-proxy",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "TH",
      "O": "system:node-proxier",
      "OU": "Kubernetes The Hard Way CentOS",
      "ST": "Bangkok"
    }
  ]
}
EOF

cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -profile=kubernetes \
  kube-proxy-csr.json | cfssljson -bare kube-proxy

mv kube-proxy-key.pem kube-proxy.key
mv kube-proxy.pem kube-proxy.crt
}


```
### สร้าง Scheduler Client Key Pair
```

{

cat > kube-scheduler-csr.json <<EOF
{
  "CN": "system:kube-scheduler",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "TH",
      "O": "system:kube-scheduler",
      "OU": "Kubernetes The Hard Way CentOS",
      "ST": "Bangkok"
    }
  ]
}
EOF

cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -profile=kubernetes \
  kube-scheduler-csr.json | cfssljson -bare kube-scheduler

mv kube-scheduler-key.pem kube-scheduler.key
mv kube-scheduler.pem kube-scheduler.crt
}
```
### สร้าง Kubernetes API Server Key Pair
```
{
KUBERNETES_HOSTNAMES=master0,master1,master2,kubernetes,kubernetes.default,kubernetes.default.svc,kubernetes.default.svc.cluster,kubernetes.svc.cluster.local
cat > kubernetes-csr.json <<EOF
{
  "CN": "kubernetes",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "TH",
      "O": "Kubernetes",
      "OU": "Kubernetes The Hard Way CentOS",
      "ST": "Bangkok"
    }
  ]
}
EOF

cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -hostname=10.96.0.1,10.32.0.1,192.168.254.60,192.168.254.61,192.168.254.62,192.168.254.63,127.0.0.1,${KUBERNETES_HOSTNAMES} \
  -profile=kubernetes \
  kubernetes-csr.json | cfssljson -bare kubernetes

mv kubernetes-key.pem kube-apiserver.key
mv kubernetes.pem kube-apiserver.crt
}
```
### สร้าง Service Accouht Key Pair
```
{

cat > service-account-csr.json <<EOF
{
  "CN": "service-accounts",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "TH",
      "O": "Kubernetes",
      "OU": "Kubernetes The Hard Way CentOS",
      "ST": "Bangkok"
    }
  ]
}
EOF

cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -profile=kubernetes \
  service-account-csr.json | cfssljson -bare service-account

mv service-account-key.pem service-account.key
mv service-account.pem service-account.crt
}
```
### สร้าง etcd Server Key Pair
```
{
ETCD_HOSTNAMES=etcd0,etcd1,etcd2

cat > etcd-server-csr.json <<EOF
{
  "CN": "etcd-server",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "TH",
      "O": "Kubernetes",
      "OU": "Kubernetes The Hard Way CentOS",
      "ST": "Bangkok"
    }
  ]
}
EOF

cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -hostname=192.168.254.61,192.168.254.62,192.168.254.63,127.0.0.1,${ETCD_HOSTNAMES} \
  -profile=kubernetes \
  etcd-server-csr.json | cfssljson -bare etcd-server

mv etcd-server-key.pem etcd-server.key
mv etcd-server.pem etcd-server.crt
}
```
**Next>** [สร้าง Kubernetes Configuration Files สำหรับ Authentication](03-generating-kubenetes-configuration-file.md)

**<Prev** [รายละเอียดในการติดตั้ง](01-prerequisites.md)
