# รายละเอียดในการติดตั้ง
ในขั้นตอนนี้เป็นขั้นตอนการเตรียม Virtual Machine สำหรับติดตั้ง Kubernetes และ Admin Workstation สำหรับเป็นเครื่องมือในการเตรียมข้อมูล และใช้ติดตั้ง Kubernetes ให้พร้อมในการติดตั้ง Kubernetes แบบ The Hard Way 
## Admin Workstation
ในกรณีของผู้เขียนใช้ macOS version 10.15.6 เป็นเครื่องหลักในการติดตั้ง ดังนั้นรายละเอียดในส่วนนี้ ก็จะอ้างอิงกับ macOS แต่เครื่องมือที่จำเป็นก็สามารถติดตั้งในระบบปฏิบัติการ Linux ได้ เครื่องมือจะถูกติดตั้งผ่าน [Homebrew](https://brew.sh/) ที่เป็นเครื่องมือ Package Manager ที่ช่วยอำนวยความสะดวกในการติดตั้ง software เพิ่มเติมใน macOS
### ติดตั้ง cfssl และ kubectl 
```
brew install cfssl kubectl
```
> ตรวจสอบการติดตั้ง
```
$ cfssl version
Version: dev
Runtime: go1.14
$ cfssljson --version 
Version: dev
Runtime: go1.14
$ kubectl version --client 
Client Version: version.Info{Major:"1", Minor:"19", GitVersion:"v1.19.2", GitCommit:"f5743093fd1c663cb0cbc89748f730662345d44d", GitTreeState:"clean", BuildDate:"2020-09-16T21:51:49Z", GoVersion:"go1.15.2", Compiler:"gc", Platform:"darwin/amd64"}
```
นอกจาก software ที่ต้องติดตั้งเพิ่มเติม ก็ยังใช้ software ที่ถูกติดตั้งมาพร้อมกับ macOS อยู่แล้วได้แก่ terminal, ssh client, ssh-keygen
### สร้าง Key Pair สำหรับ Authentication เครื่อง Virtual Machine
```
$ ssh-keygen -t ed25519
Generating public/private ed25519 key pair.
Enter file in which to save the key (/Users/drs/.ssh/id_ed25519): 
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_ed25519.
Your public key has been saved in id_ed25519.pub.
The key fingerprint is:
SHA256:4Ox72ZR/DwqpVCu0LtmN+KQ0BWGgyYI7G6x9QejOYHs drs@Tyche6c.local
The key's randomart image is:
+--[ED25519 256]--+
|    ..o          |
|.. + . .         |
|o = . o          |
|.+ . o o         |
|=o. . o S ..     |
|oO.  o o ooo     |
|o.+E. ++=*+.  .  |
|  .. .+B=oo..... |
|      o++   .. ..|
+----[SHA256]-----+
```
## Virtual Machine
ใน lab ของผู้เขียนใช้ [oVirt](https://www.ovirt.org/) เป็นเครื่องมือในการจัดการ ซึ่งแต่ละคนอาจจะใช้เครื่องมือที่ต่างกัน เช่น VirtualBox, VMWare Workstation, VMWare vSphere, VMWare vCenter, OpenStack, Azure Stack หรือจะเป็น Instance ที่ Public Cloud ก็ได้ ในการติดตั้งครั้งนี้ต้องการ Virtual Machine ทั้งหมด 6 เครื่องดังนี้
|Item|Hostname|IP|Function|
|----|--------|--|--------|
|1|api.example.com|192.168.254.60|Loadbalancer|
|2|master0.example.com|192.168.254.61|Master|
|3|master1.example.com|192.168.254.62|Master|
|4|master2.example.com|192.168.254.63|Master|
|5|node0.example.com|192.168.254.64|Worker|
|6|node1.example.com|192.168.254.65|Worker|
> - ทุกเครื่องมี 2 vCPU, Memory 2 GB, HDD 10 GB
> - ทุกเครื่องมีระบบปฏิบัติการ CentOS 8.2
> - ติดตั้ง Docker ที่ Worker Node ทั้งสองเครื่อง
> - ทุกเครื่องสามารถใช้ SSH Key-Based Authentication ได้
### ตัวอย่างการโอนถ่าย Public Key ไปยังเครื่อง Virtual Machine เพื่อให้สามารถทำ SSH Key-Based Authentication ได้
```
$ ssh-copy-id root@192.168.254.60
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/Users/drs/.ssh/id_ed25519.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@192.168.254.60's password:

Number of key(s) added:        1

Now try logging into the machine, with:   "ssh 'root@192.168.254.60'"
and check to make sure that only the key(s) you wanted were added.
```
### ตรวจสอบ Docker ที่ติดตั้งใน Worker Node
```
# docker version
Client: Docker Engine - Community
 Version:           19.03.13
 API version:       1.40
 Go version:        go1.13.15
 Git commit:        4484c46d9d
 Built:             Wed Sep 16 17:03:45 2020
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.13
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       4484c46d9d
  Built:            Wed Sep 16 17:02:21 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.2.13
  GitCommit:        7ad184331fa3e55e52b890ea95e65ba581ae3429
 runc:
  Version:          1.0.0-rc10
  GitCommit:        dc9208a3303feef5b3839f4323d9beb36df0a9dd
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
```
**Next>** [แนวคิดในการสร้าง TLS Certicate ใน Kubernetes และ สร้าง Certificate Authority กับ TLS Certificate](02-generating-tls-certificate.md)
