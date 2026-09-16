# 🔐 อธิบายคำสั่ง `journalctl _COMM=sudo -n 10 --no-pager`

คำสั่ง:

```bash
sudo journalctl _COMM=sudo -n 10 --no-pager
```

## 🧩 แยกความหมายของแต่ละส่วน

### 1. `sudo`

ใช้สิทธิ์ของ `root` เพื่ออ่าน Systemd Journal

```bash
sudo
```

มีประโยชน์ในกรณีที่ผู้ใช้ปัจจุบันไม่มีสิทธิ์อ่าน Log บางส่วนของระบบ

---

### 2. `journalctl`

คำสั่งสำหรับอ่านและค้นหา Log ที่เก็บโดย **systemd-journald**

```bash
journalctl
```

สามารถใช้ค้นหา Log ตามเวลา, service, process, priority และ field ต่าง ๆ ได้

---

### 3. `_COMM=sudo`

เป็นเงื่อนไขสำหรับกรอง Log โดยใช้ค่า **`_COMM` (Command Name)**

```bash
_COMM=sudo
```

หมายความว่า:

> แสดงเฉพาะ Log ที่มีชื่อโปรแกรม/คำสั่งเป็น `sudo`

ตัวอย่างเช่น หากมีการใช้:

```bash
sudo systemctl restart nginx
sudo cat /etc/shadow
sudo useradd testuser
```

รายการที่เกี่ยวข้องกับ process `sudo` อาจถูกแสดงออกมา

---

### 4. `-n 10`

กำหนดให้แสดง Log จำนวน **10 รายการล่าสุด**

```bash
-n 10
```

เทียบเท่ากับ:

```bash
--lines=10
```

ดังนั้น:

```bash
journalctl _COMM=sudo -n 10
```

หมายถึง:

> แสดง Log ของ `sudo` จำนวน 10 รายการล่าสุด

---

### 5. `--no-pager`

ปิดการส่งผลลัพธ์ผ่าน Pager เช่น `less`

```bash
--no-pager
```

โดยปกติ `journalctl` อาจเปิดผลลัพธ์ผ่าน `less` ทำให้ต้องกด `q` เพื่อออก

เมื่อใช้:

```bash
--no-pager
```

ผลลัพธ์จะถูกแสดงออกทาง Terminal โดยตรง

เหมาะสำหรับ:

- Script
- SSH
- Copy Log
- ส่งผลลัพธ์เข้าไฟล์
- SOC / Threat Hunting

---

# 🛡️ สรุปการทำงาน

คำสั่ง:

```bash
sudo journalctl _COMM=sudo -n 10 --no-pager
```

ทำงานตามลำดับดังนี้:

1. ใช้สิทธิ์ `root`
2. อ่าน Log จาก `systemd-journald`
3. Filter เฉพาะ Process ที่มี `_COMM=sudo`
4. แสดง 10 รายการล่าสุด
5. ไม่เปิด Pager

พูดง่าย ๆ คือ:

> **"ขอดู Log ล่าสุด 10 รายการของคำสั่ง `sudo` และแสดงออกทาง Terminal โดยตรง"**

---

# 🔎 ตัวอย่างการใช้งานด้าน Security

ใช้ตรวจสอบการใช้ `sudo` ล่าสุด:

```bash
sudo journalctl _COMM=sudo -n 10 --no-pager
```

ตัวอย่างผลลัพธ์:

```text
Sep 16 08:41:22 lab-linux-node-01 sudo[2451]: itk : TTY=pts/0 ; PWD=/home/itk ; USER=root ; COMMAND=/usr/bin/systemctl restart nginx
Sep 16 08:43:10 lab-linux-node-01 sudo[2512]: itk : TTY=pts/0 ; PWD=/home/itk ; USER=root ; COMMAND=/usr/bin/firewall-cmd --reload
```

จาก Log สามารถนำไปตรวจสอบต่อได้ เช่น:

- ใครใช้ `sudo`
- ใช้ `sudo` เมื่อเวลาใด
- ใช้คำสั่งอะไร
- เปลี่ยนไปเป็น User ใด
- มีการใช้สิทธิ์ `root` หรือไม่

---

# 🎯 สำหรับ Threat Hunting

สามารถขยายจาก 10 รายการเป็นช่วงเวลาที่ต้องการได้ เช่น:

## ดู sudo ใน 1 ชั่วโมงล่าสุด

```bash
sudo journalctl _COMM=sudo --since "1 hour ago" --no-pager
```

## ดู sudo วันนี้

```bash
sudo journalctl _COMM=sudo --since today --no-pager
```

## ดู sudo เฉพาะช่วงเวลาที่กำหนด

```bash
sudo journalctl _COMM=sudo \
  --since "2026-09-16 08:00:00" \
  --until "2026-09-16 09:00:00" \
  --no-pager
```

## ดูเฉพาะคำสั่ง sudo ล่าสุด 50 รายการ

```bash
sudo journalctl _COMM=sudo -n 50 --no-pager
```

---

# ⚠️ ข้อควรรู้

`_COMM=sudo` เป็นการกรองจาก **ชื่อ Command ของ Process** ไม่ใช่การค้นหาข้อความทุกอย่างที่เกี่ยวข้องกับคำว่า `sudo`

ดังนั้นจึงแตกต่างจาก:

```bash
sudo journalctl | grep sudo
```

โดย:

- `_COMM=sudo` → ใช้ Journal field ในการ Filter
- `grep sudo` → ค้นหาข้อความที่มีคำว่า `sudo`

สำหรับการค้นหา Log ของ Process โดยตรง การใช้ Journal field เช่น `_COMM=sudo` จะมีความชัดเจนกว่าการใช้ `grep`

---

# 🔐 Security / Threat Hunting Insight

คำสั่งนี้เหมาะสำหรับการตรวจสอบ **Privilege Escalation และ Administrative Activity** ใน Linux เพราะสามารถดูเหตุการณ์ที่เกี่ยวข้องกับการเรียกใช้ `sudo` ได้อย่างรวดเร็ว

ตัวอย่าง Workflow:

```bash
# 1. ดู sudo ล่าสุด
sudo journalctl _COMM=sudo -n 10 --no-pager

# 2. ดู sudo วันนี้
sudo journalctl _COMM=sudo --since today --no-pager

# 3. ตรวจสอบช่วงเวลาที่สงสัย
sudo journalctl _COMM=sudo \
  --since "2026-09-16 08:00:00" \
  --until "2026-09-16 09:00:00" \
  --no-pager
```

จากนั้นนำข้อมูลไปตรวจสอบต่อว่า:

- 👤 User ใดเป็นผู้เรียกใช้ `sudo`
- ⏰ เหตุการณ์เกิดขึ้นเวลาใด
- 🎯 คำสั่งใดถูก Execute
- 🔑 มีการเปลี่ยนสิทธิ์เป็น `root` หรือ User อื่นหรือไม่
- 🚨 คำสั่งนั้นสอดคล้องกับงานปกติหรือไม่
- 🔍 มีพฤติกรรมที่อาจเกี่ยวข้องกับ Privilege Escalation หรือไม่

> **สรุป:** `journalctl _COMM=sudo` เป็นวิธีที่สะดวกสำหรับการ Filter Journal ตาม Process Name เพื่อใช้ตรวจสอบกิจกรรม `sudo` โดยเฉพาะในงาน Linux Security และ Threat Hunting
