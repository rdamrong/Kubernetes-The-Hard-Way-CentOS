# เคล็ดไม่ลับ เล็ก ๆ น้อย ๆ
ในขั้นตอนของการตั้ง master node 3 เครื่อง หรือ worker node 2 เครื่อง ก็จะเป็นชุดคำสั่งเดียวกัน ที่ต้องทำซ้ำ ๆ กัน ผู้เขียนจะติดตั้ง tmux แล้วเปิด 3 หรือ 2 pane เพื่อ remote access ไปยัง Virtual Machine แล้ว จากนั้นกด PREF + :setw synchronize-panes on จะทำให้เราพิมพ์คำสั่งครั้งเดียว เพื่อสั่งไปยัง Virtual Machine ได้
![screehshot](https://github.com/rdamrong/Kubernetes-The-Hard-Way-CentOS/blob/master/images/ss1.png)
**Next>** [โอนถ่ายไฟล์ที่สร้างไปยัง Virtual Machine](06-transfer-file.md)

**<Prev** [สร้าง Data Encryption Config และ Key](04-generating-data-encryption-key.md)
