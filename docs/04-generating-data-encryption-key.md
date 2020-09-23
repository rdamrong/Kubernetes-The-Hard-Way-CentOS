# สร้าง Data Encryption Config และ Key
ข้อมูลที่ Kubernetes API Server เขียนข้อมูลเก็บไว้ใน etcd server ในการเรียกใช้งาน Kubernetes API Server สามารถกำหนดให้เข้ารหัสลับข้อมูลที่ถูกจัดเก็บได้ เพื่อป้องกันข้อผิดพลาดที่อาจจะมีการเข้าถึงข้อมูลที่ etcd server โดยตรง ดังนั้งจึงต้องสร้าง Key เพื่อใช้ในการเข้ารหัสลับ, เก็บ key ไว้ที่ object ที่ชื่อว่า EncryptionConfig และ กำหนดให้ kube-apiserver เรียกใช้ EncryptionConfig ด้วย option  `--encryption-provider-config`
## สร้าง Key ที่ใช้ในการเข้ารหัส
ในการสร้าง Key จะใช้วิธีการอ่านข้อมูลจาก /dev/urandom และ encode ด้วย base64
```
{
ENCRYPTION_KEY=$(head -c 32 /dev/urandom | base64)
}
```
## สร้าง EncryptionConfig
```
{
cat > encryption-config.yaml <<EOF
kind: EncryptionConfig
apiVersion: v1
resources:
  - resources:
      - secrets
    providers:
      - aescbc:
          keys:
            - name: key1
              secret: ${ENCRYPTION_KEY}
      - identity: {}
EOF
}
```
**Next>** [เคล็ดไม่ลับ เล็ก ๆ น้อย ๆ](05-tip-n-trick.md)

**<Prev** [สร้าง Kubernetes Configuration Files สำหรับ Authentication](03-generating-kubenetes-configuration-file.md)
