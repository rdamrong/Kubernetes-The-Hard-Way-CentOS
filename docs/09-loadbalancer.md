
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
