# 🔐 การใช้ `systemctl unmask auditd` ก่อนเปิดใช้งาน Auditd

คำสั่งที่ใช้:

    sudo systemctl unmask auditd
    sudo systemctl enable auditd
    sudo service auditd restart

## 🛡️ ทำไมต้องใช้ `unmask` ก่อน?

การใส่คำสั่ง

    sudo systemctl unmask auditd

ไว้เป็นขั้นตอนแรก มีจุดประสงค์เพื่อ **การันตีว่า `auditd` ไม่ได้อยู่ในสถานะ `Masked`** ค้างมาจากการตั้งค่าหรืออิมเมจระบบเดิม

เมื่อ Service ถูก `Mask` ระบบ `systemd` จะไม่อนุญาตให้ Service นั้นถูก Start ตามปกติ และอาจทำให้คำสั่งที่เกี่ยวข้องกับการเปิดใช้งาน Service เกิด Error ได้

ดังนั้นการใช้ `unmask` ก่อน จึงเป็นการ **ปลดสถานะ Masked** และทำให้สามารถดำเนินการในขั้นตอนถัดไปได้

---

## 🔓 1. `systemctl unmask auditd`

    sudo systemctl unmask auditd

ใช้สำหรับ **ยกเลิกสถานะ Masked** ของ `auditd`

หาก `auditd` ถูก Mask ไว้ เช่น:

    auditd.service

อาจมีการชี้ไปยัง:

    /etc/systemd/system/auditd.service -> /dev/null

ซึ่งหมายความว่า `systemd` ตั้งใจป้องกันไม่ให้ Service นี้ถูก Start

คำสั่ง `unmask` จะนำสถานะดังกล่าวออก

### 🎯 ประโยชน์

- 🔓 ปลดสถานะ `Masked`
- 🛡️ ป้องกันปัญหาจาก Configuration เดิม
- 💿 รองรับกรณีที่ระบบถูกสร้างจาก Image หรือ Template
- ⚙️ ทำให้สามารถ Enable/Start Service ได้ตามปกติ

---

## 🚀 2. `systemctl enable auditd`

    sudo systemctl enable auditd

ใช้สำหรับกำหนดให้ `auditd` **เริ่มทำงานโดยอัตโนมัติเมื่อระบบ Boot**

โดยทั่วไปจะสร้าง Symbolic Link สำหรับการเริ่ม Service ตาม Target ที่เกี่ยวข้องกับการ Boot

### 🎯 ประโยชน์

    System Boot
         │
         ▼
      systemd
         │
         ▼
    auditd.service
         │
         ▼
    Audit Logging ทำงาน

---

## 🔄 3. `service auditd restart`

    sudo service auditd restart

ใช้สำหรับ **Restart `auditd`** เพื่อให้ Service กลับมาทำงานใหม่หลังจากมีการเปลี่ยนแปลง Configuration

ตัวอย่างเช่น:

    แก้ไข auditd.conf
           │
           ▼
      Restart auditd
           │
           ▼
    โหลด Configuration ใหม่
           │
           ▼
    Audit Logging ทำงานด้วยค่าที่กำหนด

> 💡 บนระบบ Linux รุ่นใหม่ `systemctl` เป็น interface หลักของ `systemd` แต่ `service` ยังคงมีไว้เพื่อ compatibility ในหลายระบบ

---

## 🧩 ลำดับการทำงาน

แนะนำให้เข้าใจลำดับคำสั่งเป็น:

    🔓 unmask
        │
        │ ตรวจสอบ/ปลดสถานะ Masked
        ▼
    ⚙️ enable
        │
        │ กำหนดให้เริ่มอัตโนมัติเมื่อ Boot
        ▼
    🔄 restart
        │
        │ Restart Service และโหลด Configuration
        ▼
    🛡️ auditd ทำงาน

---

## ✅ สรุป

การใช้:

    sudo systemctl unmask auditd

ก่อน `enable` และ `restart` มีจุดประสงค์เพื่อ **ทำให้แน่ใจว่า Service ไม่ได้ติดสถานะ `Masked`**

จึงช่วยลดโอกาสเกิด Error จากสถานะของ Service ที่ถูกตั้งค่ามาจาก:

- 💿 OS Image เดิม
- 🖥️ VM Template
- ⚙️ Configuration ก่อนหน้า
- 🛠️ การ Hardening ระบบ
- 🔒 การตั้งค่า `systemd` ที่เคย Mask Service ไว้

### 📌 คำสั่งแบบครบชุด

    sudo systemctl unmask auditd
    sudo systemctl enable auditd
    sudo service auditd restart

> ⚠️ หมายเหตุ: `unmask` ไม่ได้แปลว่า Service ถูก Start แล้ว แต่เป็นการ **ปลดข้อจำกัดของสถานะ Masked** เพื่อให้สามารถ Enable/Start/Restart ได้ตามปกติ
