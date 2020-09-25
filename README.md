> ເອກະສານສະບັບນີ້ ໄດ້ແປມາຈາກພາສາໄທຂອງທ່ານ https://github.com/rdamrong/Kubernetes-The-Hard-Way-CentOS ອ້າງອິງຈາກ Kubernetes The Hard Way ທີ່ພັດທະນາໂດຍ [Kelsey Hightower](https://github.com/kelseyhightower/kubernetes-the-hard-way) ແລະອີກສະບັບທີ່ຖືກພັດທະນາໂດຍ [Mumshad Mannambeth](https://github.com/mmumshad/kubernetes-the-hard-way)
# Kubernetes The Hard Way - CentOS
ໃນ Repository ນີ້ເປັນເອກະສານແລະຂໍ້ມູນຕິດຕັ້ງ Kubernetes ແບບ Kubernetes The Hard Way ຕາມແນວທາງຂອງ Kelsey Hightower ຈາກຂໍ້ມູນໃນວັນທີ່ 20 ກັນຍາ 2563 ແບບຂອງ Kelsey Hightower ຈະອ້າງຖິງລະບົບທີ່ຖືກສ້າງຢູ່ໃນ Google Cloud Platform ທັງໝົດ ໂດຍໃຊ້ Kubernetes v1.18.6 ແລະຕິດຕັ້ງເທິງລະບົບປະຕິບັດການ Ubuntu  ຜູ້ຂຽນໄດ້ພັດທະນາແລະປັບປຸງໃຫ້ທັນສະໄໝຫລາຍຂຶ້ນ ແລະປັບໃຫ້ໃຊ້ຢູ່ເທິງພື້ນຖານຂອງລະບົບປະຕິບັດການ CentOS 8 ໂດຍມີເຈດນາຈະເຜີຍແຜ່ໃຫ້ກັບບຸກຄົນທົ່ວໄປທີ່ສົນໃຈສຶກສາການຕິດຕັ້ງ Kubernetes 
# Copyright

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.
# ລາຍລະອຽດຂອງ Kubernetes Cluster
- Kubernetes 1.19.0
- Docker 19.3.13
- cni v0.8.7
- etcd v3.4.13
- NGINX v1.14.1 (Loadbalancer)
- ຊຸດ IP ພາຍໃນ Cluster 10.96.0.0/24
- Cluster DNS 10.96.0.10
# ສານບັນ Kubernetes The Hard Way - CentOS
[01. ລາຍລະອຽດໃນການຕິດຕັ້ງ](docs/01-prerequisites.md)

[02. ແນວຄິດໃນການສ້າງ TLS Certicate ໃນ Kubernetes ແລະ ສ້າງ Certificate Authority ກັບ TLS Certificate](docs/02-generating-tls-certificate.md)

[03. ສ້າງ Kubernetes Configuration Files ສຳລັບ Authentication](docs/03-generating-kubenetes-configuration-file.md)

[04. ສ້າງ Data Encryption Config ແລະ Key](docs/04-generating-data-encryption-key.md)

[05. ເຄັດບໍ່ລັບ ນ້ອຍ ໆ ນ້ອຍ ໆ](docs/05-tip-n-trick.md)

[06. ໂອນຖ່າຍໄຟລ໌ທີ່ສ້າງໄປໄວ້ໃນ Virtual Machine](docs/06-transfer-file.md)

[07. ຕິດຕັ້ງ etcd](docs/07-install_etcd_cluster.md)

[08. ຕິດຕັ້ງ Kubernetes Control Plane](docs/08-install_kubernetes_control_plane.md)

[09. ຕິດຕັ້ງ Loadbalancer ສຳລັບ master node](docs/09-loadbalancer.md)

[10, ຕິດຕັ້ງ Kubernetes Worker Nodes](docs/10-install-worker-node.md)

[11. Kubernetes Object Authorization](docs/11-kubernetes-object-authorization.md)

[12. ສ້າງ kubeconfig ສຳລັບ remote access](docs/12-kubectl-remote-access.md)

[13. ຕິດຕັ້ງ Network ສຳລັບ Pod](docs/13-pod-network.md)

[14. ຕິດຕັ້ງ DNS ສຳລັບ Services ແລະ Pods](docs/14-dns-for-services-and-pods.md)

[15. ທົດສອບການ-ເຮັດ-ວຽກງານ](docs/15-test.md)

# ຂອບໃຈ
- ຂອບໃຈໂອກາດທີ່ໄດ້ຮຽນຮູ້ຈາກ MFEC Public Company Limtied
- ຂອບໃຈ @Sirinat Oam Paphatsirinatthi ແລະ @Saritrat Jirakulphondchai ຈາກ @KubeOps Skills ທີ່ພາຍ່າງກ້າວແຮກໃຫ້ຮຽນຮູ້
- ຂອບໃຈ @Sukkarin ທີ່ເປັນໝູ່ລົມ ເພື່ອນຄິດ ຊ່ວຍຫາຄຳຕອບຕອນທີ່ຄິດບໍ່ອອກ
- ຂອບໃຈທຸກຄົນທີ່ເປັນກຳລັງໃຈ ແລະຟັງສຽງຈົ່ມໃນ Facebook

# ສຸດທ້າຍ
ເອກະສານຊຸດນີ້ຂຽນຈາກການລອງຜິດ ລອງຖືກ ຕາມເອກະສານທີ່ອ້າງອິງຂອງຜູ້ຂຽນເອງ ແລະເຫັນວ່າເອກະສານຊຸດນີ້ຍັງບໍ່ມີຊຸດທີ່ເປັນພາສາໄທຈຶ່ງໄດ້ພັດທະນາເອກະສານຊຸດນີ້ຂຶ້ນມາ ຜູ້ຂຽນຍິນດີຮັບຟັງທຸກຄວາມຄິດເຫັນໃນເຊີງສ້າງສັນ ເພື່ອຊ່ວຍກັນພັດທະນາເອກະສານສະບັບນີ້ໃຫ້ເປັນປະໂຫຍດກັບຜູ້ທີ່ຕ້ອງການສຶກສາ

ດ້ວຍຈິດຄານວະ

# ຂອບໃຈທີມງານ LaoITDev ສຳລັບການແປພາສາລາວ
