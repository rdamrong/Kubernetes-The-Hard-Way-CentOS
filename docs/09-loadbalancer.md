# ติดตั้ง Loadbalancer สำหรับ master node
master node แต่ละตัวถูกออกแบบให้สามารถทำงานแยกกันได้อย่างอิสระ สามารถเชื่อมต่อไปใช้งานที่ master node ตัวไหนก็ได้ ดังนั้นเพื่อให้สะดวกกับการเชื่อมต่อไปยัง master node จึงนิยมให้มี loadbalancer ทำหน้าที่จัดการ loadbalancer ไปยัง master node แต่ละตัว ซึ่งผู้เขียนเลือกใช้ NGINX มาทำหน้าที่เป็น loadbalancer

## ขั้นตอนการติดตั้ง NGINX [loadbalancer node]
```
yum install -y nginx
echo " 
stream{
  upstream kubernetes {
      server 192.168.254.61:6443;
      server 192.168.254.62:6443;
      server 192.168.254.63:6443;
  }
  server {
      listen 6443;
      proxy_pass kubernetes;
  }
}
" >> /etc/nginx/nginx.conf
systemctl enable --now nginx
```
**Next>** [ติดตั้ง Kubernetes Worker Nodes](10-install-worker-node.md)

**<Prev** [ติดตั้ง Kubernetes Control Plane](08-install_kubernetes_control_plane.md)
