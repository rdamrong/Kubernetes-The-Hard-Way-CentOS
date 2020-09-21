# ติดตั้ง Network สำหรับ Pod
ในการเชื่อมต่อผ่าน network ของ pod ภายใน cluster มีหลายตัวเลือกให้เลือกใช้ ในเอกสารนี้เลือก [weave](https://www.weave.works/docs/net/latest/kubernetes/kube-addon/) เป็นตัวอย่างในการติดตั้ง ซี่งต้องทำงานร่วมกัน Container Network Interface (CNI)
## ติดตั้ง Container Network Interface (CNI) [all worker]
```
wget https://github.com/containernetworking/plugins/releases/download/v0.8.7/cni-plugins-linux-amd64-v0.8.7.tgz
tar xvfz cni-plugins-linux-amd64-v0.8.7.tgz  --directory /opt/cni/bin
```
## ติดตั้ง weave [master0]
```
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```
ตรวจสอบการติดตั้ง weave [master0]
```
kubectl get pods -n kube-system
```
> จะพบว่ามี pod ที่ชื่อว่า weave-net-xxxxx ที่มีสถานะ Running ทำงานอยู่
