> เอกสารฉบับนี้ อ้างอิงจาก Kubernetes The Hard Way ที่ถูกพัฒนาโดย Kelsey Hightower และอีกฉบับที่ถูกพัฒนาโดย Mumshad Mannambeth 
# Kubernetes The Hard Way - CentOS
ใน Repository นี้เป็นเอกสารและข้อมูลติดตั้ง Kubernetes แบบ Kubernetes The Hard Way ตามแนวทางของ Kelsey Hightower จากข้่อมูลในวันที่ 20 กันยายน 2564 แนวของ Kelsey Hightower จะอัางถึงระบบที่ถูกสร้างอยู่ใน Google Cloud Platform ทั้งหมด โดยใช้ Kubernetes v1.18.6 และติดตั้งบนระบบปฏิบัติการ Ubuntu  ผู้เขียนได้พัฒนาและปรับปรุงให้ทันสมัยมากขึ้น และปรับให้ใช้อยู่บนพื้นฐานของระบบปฏิบัติการ CentOS 8 โดยมีเจตนาจะเผยแพร่ให้กับบุคคลทั่วไปที่สนใจศึกษาการติดตั้ง Kubernetes 
# รายละเอียดของ Kubernetes Cluster
- Kubernetes 1.19.0
- Docker 19.3.x
- cni v0.8.7
- etcd v3.4.13
- NGINX v1.19 (Loadbalancer)
- ชุด IP ภายใน Cluster 10.96.0.0/24
