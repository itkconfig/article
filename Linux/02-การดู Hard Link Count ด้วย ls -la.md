# 🔗 การดู Hard Link Count ด้วย `ls -la`

จากผลลัพธ์ของคำสั่ง `ls -la`

    [itk@lab-linux-node-01 dir2]$ ls -la

    total 4

    drwxr-xr-x. 2 itk itk   24 Sep 17 14:04 .
    drwx------. 4 itk itk 4096 Sep 18 11:51 ..
    -rw-r--r--. 1 itk itk    0 Sep 17 14:04 index.html

---

## 📌 Hard Link Count อยู่ตรงไหน?

ในผลลัพธ์ของ `ls -la` ตัวเลข **คอลัมน์ที่ 2** คือจำนวน **Hard Link Count**

    Permissions    Link Count    Owner  Group  Size  Date              Name
    drwxr-xr-x.    2             itk    itk    24    Sep 17 14:04      .
    drwx------.    4             itk    itk    4096  Sep 18 11:51      ..
    -rw-r--r--.    1             itk    itk    0     Sep 17 14:04      index.html

---

# 1. 📁 `.` — Directory ปัจจุบัน

    drwxr-xr-x. 2 itk itk 24 Sep 17 14:04 .

**Link Count = 2**

### ทำไมถึงเป็น 2?

สำหรับ Directory ที่ไม่มี Subdirectory โดยทั่วไปจะมี Hard Link หลัก ๆ 2 จุด ได้แก่

    dir2
     ├── .       → ชี้กลับมาที่ dir2 เอง
     └── dir2    → ชื่อ Directory ที่อยู่ใน Parent Directory

ดังนั้น Directory ที่ไม่มี Subdirectory จะมีค่า Link Count เป็น

    2

โดย `.` คือชื่อพิเศษที่หมายถึง **Directory ปัจจุบัน**

---

# 2. 📁 `..` — Parent Directory

    drwx------. 4 itk itk 4096 Sep 18 11:51 ..

**Link Count = 4**

ค่า Link Count ของ Directory สามารถอธิบายโดยทั่วไปได้ว่า

    2 + จำนวน Subdirectory

ดังนั้นถ้า Parent Directory มี Link Count = `4`

ก็สามารถตีความได้ว่า Parent Directory มี Subdirectory ประมาณ

    4 - 2 = 2

Directory ย่อย

ตัวอย่าง:

    Parent Directory
    ├── dir1/
    └── dir2/

แต่ละ Subdirectory จะมี `..` ที่ชี้กลับมายัง Parent Directory ทำให้ Link Count ของ Parent Directory เพิ่มขึ้น

> ⚠️ หมายเหตุ: แนวคิด `2 + จำนวน Subdirectory` ใช้ได้กับ Filesystem แบบ Unix/Linux ทั่วไป แต่รายละเอียดอาจแตกต่างกันตาม Filesystem

---

# 3. 📄 `index.html` — Regular File

    -rw-r--r--. 1 itk itk 0 Sep 17 14:04 index.html

**Link Count = 1**

ไฟล์ธรรมดาที่สร้างขึ้นมาใหม่โดยทั่วไปจะมี Hard Link Count เริ่มต้นเป็น

    1

หมายความว่าในขณะนี้มีชื่อไฟล์เพียง 1 ชื่อที่ชี้ไปยัง inode เดียวกัน

แนวคิดคือ

    index.html
         │
         ▼
       inode
         │
         ▼
      ข้อมูลไฟล์

---

# 🔗 ทดลองสร้าง Hard Link

ลองสร้าง Hard Link ด้วยคำสั่ง

    ln index.html index_backup.html

จากนั้นตรวจสอบด้วย

    ls -la

จะพบประมาณนี้

    -rw-r--r--. 2 itk itk 0 Sep 17 14:04 index.html
    -rw-r--r--. 2 itk itk 0 Sep 17 14:04 index_backup.html

สังเกตว่า Link Count ของทั้งสองไฟล์กลายเป็น

    2

---

# 🧠 ทำไมทั้งสองไฟล์จึงมีค่าเป็น 2?

เพราะ `index.html` และ `index_backup.html` เป็น **Hard Link ที่ชี้ไปยัง inode เดียวกัน**

    index.html
         │
         ├──────────┐
         │          │
         ▼          ▼
       ┌───────────────┐
       │     inode     │
       │    123456     │
       └───────────────┘
              ▲
              │
         index_backup.html

ดังนั้น

    index.html        → inode 123456
    index_backup.html → inode 123456

จึงมี Hard Link Count = `2`

---

# 🔍 ตรวจสอบด้วย `ls -li`

สามารถดู inode ได้ด้วย

    ls -li

ตัวอย่าง:

    123456 -rw-r--r--. 2 itk itk 0 Sep 17 14:04 index.html
    123456 -rw-r--r--. 2 itk itk 0 Sep 17 14:04 index_backup.html

จุดสำคัญคือ **inode number เหมือนกัน**

    123456 → index.html
    123456 → index_backup.html

แสดงว่าทั้งสองชื่อไฟล์ชี้ไปยัง inode เดียวกัน

---

# 🎯 สรุป

    ls -la

คอลัมน์ที่ 2

    │
    ▼
    Hard Link Count

## 📁 Directory

Directory ที่ไม่มี Subdirectory โดยทั่วไป:

    Link Count = 2

ประกอบด้วยแนวคิดหลัก ๆ คือ

    .       → Directory ตัวเอง
    ชื่อ Directory → Link จาก Parent Directory

ถ้ามี Subdirectory เพิ่มขึ้น Link Count ของ Parent Directory จะเพิ่มขึ้น

    Link Count ≈ 2 + จำนวน Subdirectory

---

## 📄 Regular File

ไฟล์ทั่วไปที่ยังไม่มี Hard Link เพิ่ม:

    index.html → Link Count = 1

หลังสร้าง Hard Link:

    ln index.html index_backup.html

จะกลายเป็น

    index.html        → Link Count = 2
    index_backup.html → Link Count = 2

เพราะทั้งสองชื่อชี้ไปยัง **inode เดียวกัน**

---

# 🛡️ Cybersecurity / SOC Admin Perspective

Hard Link มีความสำคัญต่อการตรวจสอบระบบ เพราะ **ชื่อไฟล์หลายชื่อสามารถชี้ไปยัง inode และข้อมูลเดียวกันได้**

จึงควรตรวจสอบด้วย

    ls -li

หรือค้นหา Regular File ที่มี Hard Link มากกว่า 1 ด้วย

    find /var -xdev -type f -links +1 -ls

คำสั่งนี้สามารถช่วยตรวจสอบไฟล์ที่มีหลาย Directory Entries ซึ่งมีประโยชน์ในการดูแลระบบ Filesystem และงาน Threat Hunting

---

# 🔐 Key Takeaway

    ls -la
       │
       └── คอลัมน์ที่ 2
                │
                ▼
         Hard Link Count
                │
        ┌───────┴────────┐
        ▼                ▼
    Directory       Regular File
        │                │
        ▼                ▼
    โดยทั่วไป 2       เริ่มต้น 1
        │                │
        │          ln เพิ่ม Hard Link
        │                │
        ▼                ▼
    เพิ่มตาม          Link Count
    Subdirectory      เพิ่มขึ้น
