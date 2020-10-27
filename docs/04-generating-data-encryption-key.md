# ສ້າງ Data Encryption Config ແລະ Key
ຂໍ້ມູນທີ່ Kubernetes API Server ຂຽນຂໍ້ມູນເກັບໄວ້ໃນ etcd server ໃນການເອີ້ນໃຊ້ງານ Kubernetes API Server ສາມາດກຳນົດໃຫ້ເຂົ້າລະຫັດລັບຂອງຂໍ້ມູນທີ່ຈັດເກັບໄວ້ໄດ້ ເພື່ອປ້ອງກັນຂໍ້ຜິດພາດທີ່ອາດຈະມີການເຂົ້າເຖິງຂໍ້ມູນ etcd server ໂດຍກົງ ດັ່ງນັ້ນຈຶ່ງຕ້ອງສ້າງ Key ເພື່ອໃຊ້ໃນການເຂົ້າລະຫັດ, ເກັບ key ໄວ້ທີ່ object ທີ່ຊື່ວ່າ EncryptionConfig ແລະ ກຳນົດໃຫ້ kube-apiserver ເອີ້ນໃຊ້ EncryptionConfig ດ້ວຍ option  `--encryption-provider-config`
## ສ້າງ Key ທີ່ໃຊ້ໃນການເຂົ້າລະຫັດ
ໃນການສ້າງ Key ຈະໃຊ້ວິທີການອ່ານຂໍ້ມູນຈາກ /dev/urandom ແລະ encode ດ້ວຍ base64
```
{
ENCRYPTION_KEY=$(head -c 32 /dev/urandom | base64)
}
```
## ສ້າງ EncryptionConfig
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
**Next>** [ເຄັດບໍ່ລັບ ທີ່ຄວນຮູ້ໄວ້ເປັນດີ](05-tip-n-trick.md)

**<Prev** [ສ້າງ Kubernetes Configuration Files ສຳລັບ Authentication](03-generating-kubenetes-configuration-file.md)
