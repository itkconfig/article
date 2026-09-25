# 🔗 วิธีตรวจสอบว่าไฟล์เป็น Symbolic Link หรือไม่ใน Linux

ในการตรวจสอบว่าไฟล์นั้นเป็น **Symbolic Link (Symlink / ทางลัด)** หรือเป็นไฟล์ปกติ ใน Linux สามารถใช้คำสั่งได้หลายวิธี โดยแต่ละคำสั่งเหมาะกับสถานการณ์ที่แตกต่างกัน

---

## 1. 🔍 ใช้คำสั่ง `ls -l`

เป็นวิธีพื้นฐานและนิยมใช้มากที่สุด เพราะสามารถมองเห็นได้ทันทีว่าไฟล์เป็น Symbolic Link หรือไม่

    ls -l /etc/pam.d/password-auth

### จุดสังเกต

หากเป็น Symbolic Link ตัวอักษรตัวแรกของ Permission จะเป็น `l`

    l

โดย `l` ย่อมาจาก **link**

และด้านท้ายจะมีเครื่องหมาย `->` ชี้ไปยังไฟล์ต้นทาง

### ตัวอย่าง

    lrwxrwxrwx. 1 root root 37 Sep 25 08:00 /etc/pam.d/password-auth -> /etc/authselect/password-auth

อ่านได้ว่า

    l
    │
    └── เป็น Symbolic Link

    password-auth
          │
          └── ชื่อ Link

    ->
    │
    └── ชี้ไปยัง

    /etc/authselect/password-auth
          │
          └── ไฟล์ต้นทาง (Target)

### 🧠 จำง่าย

    l = Symbolic Link
    - = Regular File
    d = Directory

---

## 2. 📄 ใช้คำสั่ง `file`

คำสั่ง `file` เหมาะสำหรับการตรวจสอบประเภทของไฟล์โดยตรง และอ่านผลลัพธ์ได้ง่าย

    file /etc/pam.d/password-auth

หากเป็น Symbolic Link จะเห็นข้อความประมาณ

    /etc/pam.d/password-auth: symbolic link to /etc/authselect/password-auth

หากเป็นไฟล์ปกติ อาจแสดงประเภทไฟล์ เช่น

    /etc/example.conf: ASCII text

หรือ

    /etc/example: UTF-8 Unicode text

### ⭐ จุดเด่น

`file` เหมาะมากเมื่อเราต้องการตอบคำถามว่า

    "ไฟล์นี้คือไฟล์ประเภทอะไร?"

โดยไม่ต้องอ่าน Permission เอง

---

## 3. 🔬 ใช้คำสั่ง `stat`

หากต้องการตรวจสอบรายละเอียดของไฟล์ในระดับลึก เช่น

- File type
- Inode
- Permission
- Owner
- Group
- Size
- Access time
- Modify time
- Change time

สามารถใช้

    stat /etc/pam.d/password-auth

ตัวอย่างข้อมูล

    File: /etc/pam.d/password-auth -> /etc/authselect/password-auth
    Size: 37
    Links: 1
    Inode: 123456
    Access: (0777/lrwxrwxrwx)
    Uid: (0/root)
    Gid: (0/root)

### จุดสำคัญ

ให้สังเกต Permission เช่น

    lrwxrwxrwx
    ^
    |
    └── l = Symbolic Link

ดังนั้น `stat` สามารถใช้ตรวจสอบทั้งประเภทไฟล์และรายละเอียดของไฟล์

---

## 4. 🛠️ ใช้ `test -L` ใน Shell Script

หากต้องการให้ Shell Script ตรวจสอบว่าไฟล์เป็น Symbolic Link หรือไม่ สามารถใช้ `test -L`

ตัวอย่าง

    if [ -L "/etc/pam.d/password-auth" ]; then
        echo "This is a symbolic link."
    else
        echo "This is NOT a symbolic link."
    fi

หากไฟล์เป็น Symbolic Link

    This is a symbolic link.

หากไม่ใช่

    This is NOT a symbolic link.

### ทำไมวิธีนี้สำคัญ?

เพราะ `test -L` เหมาะกับการนำไปใช้ใน Automation และ Shell Script เช่น

    if [ -L "/etc/pam.d/password-auth" ]; then
        echo "Symlink detected"
        ls -l /etc/pam.d/password-auth
    fi

---

# 🧠 Mindmap: ตรวจสอบ Symbolic Link

    ตรวจสอบ Symbolic Link
            │
            ├── 👀 ls -l
            │     │
            │     ├── ดู Permission
            │     ├── l = Link
            │     └── -> = Target
            │
            ├── 🔎 file
            │     │
            │     └── บอกประเภทไฟล์
            │
            ├── 🔬 stat
            │     │
            │     ├── File type
            │     ├── Inode
            │     ├── Owner
            │     └── Permission
            │
            └── 🤖 test -L
                  │
                  └── ใช้ใน Shell Script

---

# 📌 สรุปคำสั่ง

    # 1. ดูแบบง่ายและเห็น Link ปลายทาง
    ls -l /etc/pam.d/password-auth

    # 2. ตรวจสอบประเภทไฟล์
    file /etc/pam.d/password-auth

    # 3. ดูรายละเอียดเชิงลึก
    stat /etc/pam.d/password-auth

    # 4. ตรวจสอบใน Shell Script
    if [ -L "/etc/pam.d/password-auth" ]; then
        echo "This is a symbolic link."
    fi

---

# ⭐ System Administrator Tip

ถ้าต้องการตรวจสอบด้วยตาอย่างรวดเร็ว

    ls -l /path/to/file

ให้ดูตัวอักษรตัวแรก

    l = Symbolic Link
    - = Regular File
    d = Directory

ถ้าต้องการให้ระบบบอกประเภทไฟล์ให้เลย

    file /path/to/file

ถ้าต้องการนำไปใช้ใน Script

    [ -L "/path/to/file" ]

ดังนั้นสามารถจำง่าย ๆ ได้ว่า

    👀 ดูด้วยตา       → ls -l
    🔎 ดูประเภทไฟล์   → file
    🔬 ดูรายละเอียด   → stat
    🤖 ใช้ใน Script   → test -L

---

# 🔐 ตัวอย่างกับ PAM

สำหรับระบบ Rocky Linux / RHEL ที่ใช้ `authselect` ไฟล์ใน `/etc/pam.d/` บางไฟล์อาจถูกจัดการผ่าน Symbolic Link ดังนั้นก่อนแก้ไขไฟล์ PAM ควรตรวจสอบก่อนว่าไฟล์นั้นเป็นไฟล์จริงหรือเป็น Link

    ls -l /etc/pam.d/password-auth
    file /etc/pam.d/password-auth
    stat /etc/pam.d/password-auth

จากนั้นจึงตรวจสอบว่า Link ชี้ไปยังไฟล์ใด ก่อนตัดสินใจแก้ไข Configuration

> ⚠️ **ข้อควรระวัง:** หากไฟล์ PAM ถูกจัดการโดย `authselect` ไม่ควรแก้ไขไฟล์ที่เป็นผลลัพธ์โดยตรงโดยไม่ตรวจสอบก่อน เพราะการเปลี่ยนแปลงอาจถูกเขียนทับเมื่อมีการเปลี่ยน Profile หรือรันคำสั่งของ `authselect`

---

# 🎯 จำให้ได้ 4 คำสั่ง

    ls -l   → ดูว่าเป็น Link หรือไม่
    file    → ดูประเภทไฟล์
    stat    → ดูรายละเอียดเชิงลึก
    test -L → ตรวจสอบ Link ใน Script
