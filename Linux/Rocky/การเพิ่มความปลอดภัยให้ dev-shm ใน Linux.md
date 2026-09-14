# 🛡️ การเพิ่มความปลอดภัยให้ `/dev/shm` ใน Linux

การตั้งค่า `/dev/shm` ให้มีความปลอดภัยเป็นสิ่งสำคัญมาก โดยเฉพาะในการทำ **Linux Security / Threat Hunting Lab** เพราะ `/dev/shm` เป็นพื้นที่ Shared Memory ที่โปรแกรมต่างๆ ใช้ร่วมกัน 

หากไม่ควบคุมให้ดี ผู้ไม่ประสงค์ดีอาจใช้พื้นที่นี้นำไฟล์ Executable ไปวางแล้วรันเพื่อโจมตีระบบได้

ตัวอย่างการตั้งค่าในไฟล์ `/etc/fstab`:

```fstab
tmpfs  /dev/shm  tmpfs  defaults,nodev,nosuid,noexec,size=2G  0  0
```

---

## 🔍 บรรทัดนี้ทำอะไร?

การกำหนดบรรทัดด้านบนคือการให้ `/dev/shm` เมานต์แบบ **tmpfs** พร้อมกับกำหนด **Security Mount Options** ดังนี้:

* **`nodev`** 🚫 ห้ามสร้างหรือใช้ Device file ในพื้นที่นี้
* **`nosuid`** 🛡️ ไม่ให้สิทธิ์ SUID/SGID มีผล (ป้องกันการยกระดับสิทธิ์)
* **`noexec`** 🛑 ไม่อนุญาตให้ Execute binary จาก `/dev/shm` (ช่วยลดความเสี่ยงจากการนำไฟล์มารันจากตำแหน่งนี้)
* **`size=2G`** 💾 จำกัดพื้นที่ tmpfs ไว้สูงสุดที่ 2 GB (ป้องกัน Memory Exhaustion)
* **`defaults`** ⚙️ ใช้ค่า mount พื้นฐาน 
* **`0 0`** ⏩ ไม่ต้องทำ dump backup และไม่ต้องรัน fsck ตรวจสอบตอนบูต

---

## ✅ วิธี Apply การตั้งค่าหลังแก้ fstab

หลังจากแก้ไขไฟล์ `/etc/fstab` เสร็จแล้ว **ไม่จำเป็นต้อง Reboot** สามารถสั่งให้ระบบอ่านค่าใหม่ได้ทันที:

```bash
sudo mount -o remount /dev/shm
```

จากนั้นให้ตรวจสอบผลลัพธ์ว่าค่า Mount Options ถูกต้องหรือไม่:

```bash
findmnt /dev/shm
```

**ผลลัพธ์ที่ควรจะเห็น:**
```text
TARGET    SOURCE FSTYPE OPTIONS
/dev/shm  tmpfs  tmpfs  rw,nosuid,nodev,noexec,relatime,size=2G
```

---

## ⚠️ ข้อควรระวัง (Important Note)

> [!WARNING]
> ถ้า `/dev/shm` ถูกเมานต์อยู่แล้ว **ห้ามใช้คำสั่ง** `sudo mount /dev/shm` เด็ดขาด เพราะระบบอาจจะแจ้งเตือนว่า:
> `mount point not mounted or bad option`

**วิธีการเมานต์ที่ถูกต้อง:**

1. **ใช้ Remount** (อัปเดตเฉพาะจุด):
   ```bash
   sudo mount -o remount /dev/shm
   ```
2. **ใช้การเมานต์ทั้งหมด** (เพื่อทดสอบว่าเขียน `/etc/fstab` ถูกต้อง):
   ```bash
   sudo mount -a
   ```

---

## 📌 สรุปสั้น ๆ

| คำสั่ง / ไฟล์ | หน้าที่ |
| :--- | :--- |
| `/etc/fstab` | กำหนดค่าถาวร (มีผลทุกครั้งที่เปิดเครื่อง) |
| `mount -o remount /dev/shm` | ให้ค่าที่แก้ไขมีผลทันทีโดยไม่ต้องรีบูต |
| `mount -a` | ทดสอบและโหลดรายการจาก `fstab` ทั้งหมด |
