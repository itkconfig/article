# 🔍 อธิบายคำสั่ง `stat` สำหรับตรวจสอบ `/etc/crontab`

## 📌 คำสั่ง

stat -Lc 'Access: (%a/%A) Uid: (%u/%U) Gid: (%g/%G)' /etc/crontab

คำสั่งนี้ใช้สำหรับตรวจสอบ **Permission และ Ownership** ของไฟล์ `/etc/crontab`
โดยใช้คำสั่ง `stat` และกำหนดรูปแบบการแสดงผลที่ต้องการ

---

## 🧩 อธิบายแต่ละส่วนของคำสั่ง

### 1. `stat`

`stat` ใช้สำหรับแสดงข้อมูล Metadata ของไฟล์ เช่น

- Permission
- Owner
- Group
- File Size
- Inode
- Access Time
- Modification Time
- Change Time

ในคำสั่งนี้ใช้ `stat` เพื่อตรวจสอบ Permission และ Ownership ของ `/etc/crontab`

---

### 2. `-L`

`-L` ย่อมาจากการให้ `stat` **Follow Symbolic Links**

หมายความว่า หาก `/etc/crontab` เป็น Symbolic Link
`stat` จะติดตาม Link ไปยังไฟล์ปลายทาง

---

### 3. `-c`

`-c` ใช้กำหนดรูปแบบของ Output ที่ต้องการให้ `stat` แสดง

รูปแบบที่กำหนดคือ

'Access: (%a/%A) Uid: (%u/%U) Gid: (%g/%G)'

---

# 🔐 อธิบาย Format ที่ใช้

## `%a` — Permission แบบตัวเลข

แสดง File Permission ในรูปแบบ Octal

ตัวอย่าง

644

หมายถึง

Owner  = rw-
Group  = r--
Others = r--

หรือ

-rw-r--r--

---

## `%A` — Permission แบบ Symbolic

แสดง Permission ในรูปแบบ Symbolic

ตัวอย่าง

-rw-r--r--

ดังนั้น

%a/%A

อาจแสดงเป็น

644/-rw-r--r--

---

## `%u` — UID

แสดง User ID (UID) ของ Owner ไฟล์

ตัวอย่าง

0

UID `0` โดยทั่วไปคือ User `root`

---

## `%U` — Username

แสดงชื่อ User ที่เป็นเจ้าของไฟล์

ตัวอย่าง

root

ดังนั้น

%u/%U

อาจแสดงเป็น

0/root

---

## `%g` — GID

แสดง Group ID (GID) ของ Group ที่เป็นเจ้าของไฟล์

ตัวอย่าง

0

---

## `%G` — Group Name

แสดงชื่อ Group ที่เป็นเจ้าของไฟล์

ตัวอย่าง

root

ดังนั้น

%g/%G

อาจแสดงเป็น

0/root

---

# 🧪 ตัวอย่างผลลัพธ์

หาก `/etc/crontab` มี Permission และ Ownership เป็น

-rw-r--r-- root root

คำสั่งอาจแสดงผลเป็น

Access: (644/-rw-r--r--) Uid: (0/root) Gid: (0/root)

---

# 🔎 วิธีอ่านผลลัพธ์

จากผลลัพธ์

Access: (644/-rw-r--r--) Uid: (0/root) Gid: (0/root)

สามารถแยกความหมายได้ดังนี้

### Permission

644

หรือ

-rw-r--r--

หมายความว่า

Owner  : rw-
Group  : r--
Others : r--

---

### Owner

UID  = 0
User = root

---

### Group

GID   = 0
Group = root

---

# 🛡️ ทำไมต้องตรวจสอบ `/etc/crontab`

ไฟล์ `/etc/crontab` เป็นไฟล์สำคัญของ Linux
ที่ใช้กำหนด Scheduled Jobs หรือ Cron Jobs

ตัวอย่างเช่น

- การทำงานตามเวลาที่กำหนด
- การเรียกใช้ Script อัตโนมัติ
- การทำงานของ System Maintenance

หากผู้โจมตีสามารถแก้ไข `/etc/crontab` ได้
อาจสามารถเพิ่มคำสั่งที่ระบบจะเรียกใช้งานตามเวลาที่กำหนด

จึงอาจนำไปสู่

- Persistence
- Privilege Escalation
- Malware Execution
- Backdoor
- การรันคำสั่งด้วยสิทธิ์สูง

ดังนั้นควรตรวจสอบว่าไฟล์มี

- Permission ที่เหมาะสม
- Owner ที่ถูกต้อง
- Group ที่ถูกต้อง

ตัวอย่างที่พบได้ทั่วไปคือ

644 root root

หรือ

-rw-r--r-- root root

---

# 🔧 ตรวจสอบด้วยคำสั่งอื่น

สามารถใช้คำสั่ง

ls -l /etc/crontab

ตัวอย่างผลลัพธ์

-rw-r--r--. 1 root root 1042 Sep 15 10:00 /etc/crontab

หรือใช้

stat /etc/crontab

เพื่อดู Metadata ของไฟล์แบบละเอียด

---

# 📊 สรุป Format

| Format | ความหมาย |
|---|---|
| `%a` | Permission แบบ Octal เช่น `644` |
| `%A` | Permission แบบ Symbolic เช่น `-rw-r--r--` |
| `%u` | UID ของ Owner |
| `%U` | Username ของ Owner |
| `%g` | GID ของ Group |
| `%G` | Group Name |
| `-L` | Follow Symbolic Link |
| `-c` | กำหนดรูปแบบ Output |

---

# 🎯 ตัวอย่างคำสั่งสำหรับ Linux Hardening

stat -Lc 'Access: (%a/%A) Uid: (%u/%U) Gid: (%g/%G)' /etc/crontab

ตัวอย่างผลลัพธ์ที่ต้องการตรวจสอบ

Access: (644/-rw-r--r--) Uid: (0/root) Gid: (0/root)

---

# ✅ สรุป

คำสั่งนี้ใช้ตรวจสอบ 3 เรื่องหลักของ `/etc/crontab`

1. **File Permission**
2. **File Owner**
3. **File Group**

โดยผลลัพธ์สำคัญคือ

Access: (644/-rw-r--r--) Uid: (0/root) Gid: (0/root)

ซึ่งทำให้สามารถตรวจสอบได้อย่างรวดเร็วว่า `/etc/crontab`
มี Permission และ Ownership ตรงตาม Security Hardening ที่กำหนดหรือไม่
