# ລາຍລະອຽດໃນການຕິດຕັ້ງ
ໃນຂັ້ນຕອນນີ້ເປັນຂັ້ນຕອນການຕຽມ Virtual Machine ສຳລັບຕິດຕັ້ງ Kubernetes ແລະ Admin Workstation ສຳລັບເປັນເຄື່ອງມືໃນການກຽມຂໍ້ມູນ ແລະໃຊ້ຕິດຕັ້ງ Kubernetes ໃຫ້ພ້ອມໃນການຕິດຕັ້ງ Kubernetes ແບບ The Hard Way 
## Admin Workstation
ໃນກໍລະນີຂອງຜູ້ຂຽນໃຊ້ macOS version 10.15.6 ເປັນເຄື່ອງຫລັກໃນການຕິດຕັ້ງ ດັ່ງນັ້ນລາຍລະອຽດໃນສ່ວນນີ້ ກໍຈະອ້າງອິງກັບ macOS ແຕ່ເຄື່ອງມືທີ່ຈຳເປັນກໍສາມາດຕິດຕັ້ງໃນລະບົບປະຕິບັດການ Linux ໄດ້ ເຄື່ອງມືຈະຖືກຕິດຕັ້ງຜ່ານ [Homebrew](https://brew.sh/) ທີ່ເປັນເຄື່ອງມື Package Manager ທີ່ຊ່ວຍອຳນວຍຄວາມສະດວກໃນການຕິດຕັ້ງ software ເພິ່ມເຕິມໃນ macOS

### ຕິດຕັ້ງ cfssl ແລະ kubectl 
```
brew install cfssl kubectl
```
> ກວດສອບການຕິດຕັ້ງ
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
ນອກຈາກ software ທີ່ຕ້ອງຕິດຕັ້ງເພິ່ມເຕິມ ກໍຍັງໃຊ້ software ທີ່ຖືກຕິດຕັ້ງມາພ້ອມກັບ macOS ຢູ່ແລ້ວໄດ້ແກ່ terminal, ssh client, ssh-keygen
### ສ້າງ Key Pair ສຳລັບ Authencation ເຄື່ອງ Virtual Machine
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
ໃນ lab ຂອງຜູ້ຂຽນໃຊ້ [oVirt](https://www.ovirt.org/) ເປັນເຄື່ອງມືໃນການຈັດການ ຊຶ່ງແຕ່ລະຄົນອາດຈະໃຊ້ເຄື່ອງມືທີ່ຕ່າງກັນ ເຊັ່ນ VirtualBox, VMWare Workstation, VMWare vSphere, VMWare vCenter, OpenStack, Azure Stack ຫລືຈະເປັນ Instance ທີ່ Public Cloud ກໍໄດ້ ໃນການຕິດຕັ້ງເທື່ອນີ້ຕ້ອງການ Virtual Machine ທັງໝົດ 6 ເຄື່ອງດັ່ງນີ້
|Item|Hostname|IP|Function|
|----|--------|--|--------|
|1|api.example.com|192.168.254.60|Loadbalancer|
|2|master0.example.com|192.168.254.61|Master|
|3|master1.example.com|192.168.254.62|Master|
|4|master2.example.com|192.168.254.63|Master|
|5|node0.example.com|192.168.254.64|Worker|
|6|node1.example.com|192.168.254.65|Worker|
> - ທຸກເຄື່ອງມີ 2 vCPU, Memory 2 GB, HDD 10 GB
> - ທຸກເຄື່ອງມີລະບົບປະຕິບັດການ CentOS 8.2
> - ຕິດຕັ້ງ Docker ທີ່ Worker Node ທັງສອງເຄື່ອງ
> - ທຸກເຄື່ອງສາມາດໃຊ້ SSH Key-Based Authentication ໄດ້
### ຕົວຢ່າງການໂອນຖ່າຍ Public Key ໄປຍັງເຄື່ອງ Virtual Machine ເພື່ອໃຫ້ສາມາດເຮັດ SSH Key-Based Authentication ໄດ້
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
### ກວດສອບ Docker ທີ່ຕິດຕັ້ງໃນ Worker Node
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
**Next>** [ແນວຄິດໃນການສ້າງ TLS Certicate ໃນ Kubernetes ແລະ ສ້າງ Certificate Authority ກັບ TLS Certificate](02-generating-tls-certificate.md)
