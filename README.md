> ເອກະສານສະບັບນີ້ ອ້າງອິງຈາກ Kubernetes The Hard Way ທີ່ພັດທະນາໂດຍ [Kelsey Hightower](https://github.com/kelseyhightower/kubernetes-the-hard-way) และอีกฉบับที่ถูกพัฒนาโดย [Mumshad Mannambeth](https://github.com/mmumshad/kubernetes-the-hard-way)
# Kubernetes The Hard Way - CentOS
ใน Repository นี้เป็นเอกสารและข้อมูลติดตั้ง Kubernetes แบบ Kubernetes The Hard Way ตามแนวทางของ Kelsey Hightower จากข้่อมูลในวันที่ 20 กันยายน 2563 แนวของ Kelsey Hightower จะอัางถึงระบบที่ถูกสร้างอยู่ใน Google Cloud Platform ทั้งหมด โดยใช้ Kubernetes v1.18.6 และติดตั้งบนระบบปฏิบัติการ Ubuntu  ผู้เขียนได้พัฒนาและปรับปรุงให้ทันสมัยมากขึ้น และปรับให้ใช้อยู่บนพื้นฐานของระบบปฏิบัติการ CentOS 8 โดยมีเจตนาจะเผยแพร่ให้กับบุคคลทั่วไปที่สนใจศึกษาการติดตั้ง Kubernetes 
# Copyright

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.
# รายละเอียดของ Kubernetes Cluster
- Kubernetes 1.19.0
- Docker 19.3.13
- cni v0.8.7
- etcd v3.4.13
- NGINX v1.14.1 (Loadbalancer)
- ชุด IP ภายใน Cluster 10.96.0.0/24
- Cluster DNS 10.96.0.10
# สารบัญ Kubernetes The Hard Way - CentOS
[01. รายละเอียดในการติดตั้ง](docs/01-prerequisites.md)

[02. แนวคิดในการสร้าง TLS Certicate ใน Kubernetes และ สร้าง Certificate Authority กับ TLS Certificate](docs/02-generating-tls-certificate.md)

[03. สร้าง Kubernetes Configuration Files สำหรับ Authentication](docs/03-generating-kubenetes-configuration-file.md)

[04. สร้าง Data Encryption Config และ Key](docs/04-generating-data-encryption-key.md)

[05. เคล็ดไม่ลับ เล็ก ๆ น้อย ๆ](docs/05-tip-n-trick.md)

[06. โอนถ่ายไฟล์ที่สร้างไปยัง Virtual Machine](docs/06-transfer-file.md)

[07. ติดตั้ง etcd](docs/07-install_etcd_cluster.md)

[08. ติดตั้ง Kubernetes Control Plane](docs/08-install_kubernetes_control_plane.md)

[09. ติดตั้ง Loadbalancer สำหรับ master node](docs/09-loadbalancer.md)

[10, ติดตั้ง Kubernetes Worker Nodes](docs/10-install-worker-node.md)

[11. Kubernetes Object Authorization](docs/11-kubernetes-object-authorization.md)

[12. สร้าง kubeconfig สำหรับ remote access](docs/12-kubectl-remote-access.md)

[13. ติดตั้ง Network สำหรับ Pod](docs/13-pod-network.md)

[14. ติดตั้ง DNS สำหรับ Services และ Pods](docs/14-dns-for-services-and-pods.md)

[15. ทดสอบการทำงาน](docs/15-test.md)

# ขอบคุณ
- ขอบคุณโอกาสที่ได้เรียนรู้จาก MFEC Public Company Limtied
- ขอบคุณ @Sirinat Oam Paphatsirinatthi และ @Saritrat Jirakulphondchai จาก @KubeOps Skills ที่พาเดินก้าวแรกให้เรียนรู้
- ขอบคุณ @Sukkarin ที่เป็นเพื่อนคุย เพื่อนคิด ช่วยหาคำตอบตอนที่คิดไม่ออก
- ขอบคุณทุกคนที่เป็นกำลังใจ และฟังเสียงบ่นใน Facebook

# สุดท้าย
เอกสารชุดนี้เขียนจากการลองผิด ลองถูก ตามเอกสารที่อ้างอิงของผู้เขียนเอง และเห็นว่าเอกสารชุดนี้ยังไม่มีชุดที่เป็นภาษาไทยจึงได้พัฒนาเอกสารชุดนี้ขึ้นมา ผู้เขียนยินดีรับฟังทุกความคิดเห็นในเชิงสร้างสรรค์ เพื่อช่วยกันพัฒนาเอกสารฉบับนี้ให้เป็นประโยชน์กับผู้ที่ต้องการศึกษา

ด้วยจิตคารวะ
