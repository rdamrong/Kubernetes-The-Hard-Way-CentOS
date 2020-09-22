# แนวคิดในการสร้าง TLS Certicate ใน Kubernetes และ สร้าง Certificate Authority กับ TLS Certificate 
ในการสื่อสารกับ Kubernetes ทั้งภายในและภายนอก Cluster เรื่องความปลอดภัยในการสื่อสารเป็นเรื่องที่ได้รับการออกแบบให้มีความปลอดภัย และ [TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security) ก็เป็นวิธีการที่ถูกเลือกใช้ในการจัดการเรื่องนี้ ส่วนเรื่องของการ Authentication ของ client ในการขอเชื่อมต่อก็จะใช้วิธีการที่เรียกว่า [Mutual Authentication แบบ Certificated based](https://en.wikipedia.org/wiki/Mutual_authentication) ดังนั้นจึงจำเป็นต้องสร้าง key pair จำนวนมากในการติดตั้ง Kubernetes
## ต้องสร้าง Key Pair ที่ไหนบ้าง 
ในการใช้งาน TLS ส่วนของการ Authenitation ใน TLS ใช้ [Public-Key Cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography) ในส่วนนี้จึงสร้าง key pair โดยหลักคิดก็คือว่า component ตัวไหนทำหน้าที่เป็น Server ในการให้บริการก็ต้องสร้าง key pair ให้ และ component ไหนทำหน้าที่เป็น client ในการขอใช้บริการก็ต้องสร้าง key pair ให้
![Kubernetes and TLS Information](https://github.com/rdamrong/Kubernetes-The-Hard-Way-CentOS/blob/master/images/kube_tls_component.png)
- วงกลมสีฟ้าที่มีอักษร S แสดงในองค์ประกอบนั้นทำหน้าที่เป็น Server ที่ทำหน้าที่ให้บริการ service ในหน้าที่ของตัวเอง
- วงกลมสีฟ้าที่มีอักษร C แสดงในองค์ประกอบนั้นทำหน้าที่เป็น Client ต้องขอใช้บริการใน service ที่ต้องการ
> ในเอกสาร version แรกจะเป็นไปตามแนวทางที่ผู้เขียนอ้างอิงถึงจะมีจุดสังเกตุอยู่ 2 จุด
> - client key pair จาก kube-apiserver ไปยัง etcd จะใช้ key pair ของ etcd เองในการเชื่อมต่อ
> - client key pair จาก kube-apiserver ไปยัง kubelet จะใช้ key pair ของ kube-apiserver ที่ใช้ทำเป็น server เองในการเชื่อมต่อ
## สร้าง CA (Certficate Authority)
ประเด็นหนึ่งในการทำ Mutual Authentication แบบ Certificated based คือ Ceriticate ในฝั่ง Client จะต้องมี Digital Signature ที่ถูก sign จาก CA เดียวกับฝั่ง Server ดังนั้นการสร้าง CA key pair จึงเป็นขึ้นตอนแรก และเครื่องมือที่จะช่วยในการสร้าง key pair ก็คือ `cfssl`

