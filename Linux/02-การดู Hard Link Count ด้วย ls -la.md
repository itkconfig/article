# 🔗 คู่มือฉบับจับมือทำ: การนับ Link Count ในระบบไฟล์ Linux จาก 0

เวลาเราใช้คำสั่ง `ls -la` คอลัมน์ที่ 2 ที่เป็นตัวเลข เช่น `1`, `2`, `4` คือ **Hard Link Count**

แนวคิดง่าย ๆ คือ

> Hard Link Count = จำนวน Directory Entries ที่ชี้มายัง inode เดียวกัน

---

# 📌 ผลลัพธ์จริงจาก Home Directory

จากคำสั่ง

    ls -la

ได้ผลลัพธ์ดังนี้

    total 652
    drwx------. 4 itk  itk    4096 Sep 18 11:51 .
    drwxr-xr-x. 4 root root     35 Sep 14 09:17 ..
    -rw-------. 1 itk  itk    7367 Sep 17 17:09 .bash_history
    -rw-r--r--. 1 itk  itk      18 Oct 29  2024 .bash_logout
    -rw-r--r--. 1 itk  itk     144 Oct 29  2024 .bash_profile
    -rw-r--r--. 1 itk  itk     522 Oct 29  2024 .bashrc
    -rw-r--r--. 1 itk  itk   49152 Sep 17 17:31 before.txt
    drwxr-xr-x. 2 itk  itk      24 Sep 17 14:04 dir2
    -rw-------. 1 itk  itk     100 Sep 18 11:51 .lesshst
    -rw-r--r--. 1 itk  itk    4482 Sep 17 17:30 multi-user.target-direct.txt
    -rw-r--r--. 1 itk  itk   86016 Sep 17 17:26 multi-user.target-full.txt
    -rw-r--r--. 1 itk  itk  479233 Sep 17 17:24 multi-user.target-sorted.txt
    -rw-r--r--. 1 itk  itk    4482 Sep 17 17:29 multi-user.target.txt
    -rwxr-xr-x. 1 itk  itk    2146 Sep 11 14:16 prepare-template.sh
    drwx------. 2 itk  itk      71 Sep 17 14:55 .ssh

และคำสั่ง

    tree

ได้ผลลัพธ์

    .
    ├── before.txt
    ├── dir2
    │   └── index.html
    ├── multi-user.target-direct.txt
    ├── multi-user.target-full.txt
    ├── multi-user.target-sorted.txt
    ├── multi-user.target.txt
    └── prepare-template.sh

    2 directories, 7 files

---

# 🧠 กฎสำคัญของ Link Count

## 1. 📄 Regular File

ไฟล์ธรรมดาโดยทั่วไปเริ่มต้นที่

    Link Count = 1

ตัวอย่าง

    -rw-r--r--. 1 itk itk 49152 Sep 17 17:31 before.txt

ตัวเลข

    1

คือ Hard Link Count

เนื่องจากปัจจุบันมี Directory Entry ชื่อ `before.txt` เพียงตัวเดียวที่ชี้ไปยัง inode ของไฟล์นี้

ภาพแนวคิด

    before.txt
         │
         ▼
       inode
         │
         ▼
      file data

---

# 2. 📁 Directory

Directory จะมี Link Count เริ่มต้นโดยทั่วไปเป็น

    Link Count = 2

ตัวอย่าง

    drwxr-xr-x. 2 itk itk 24 Sep 17 14:04 dir2

ค่า `2` เกิดจาก Directory Entries หลัก ๆ คือ

    dir2
      │
      └──> inode ของ dir2

และภายใน Directory มี

    .
      │
      └──> inode ของ dir2

ดังนั้น

    dir2 → inode
    .    → inode

จึงมี

    Link Count = 2

---

# 📁 ทำไม `dir2` ถึงมี Link Count = 2?

จากคำสั่ง

    tree dir2

จะเห็น

    dir2
    └── index.html

ภายใน `dir2` มีไฟล์ `index.html`

แต่ไฟล์ธรรมดาไม่ได้เพิ่ม Link Count ของ Directory

ดังนั้น

    dir2
    ├── .
    └── index.html

จึงยังมี

    Link Count = 2

เพราะมีเพียง Directory Entry ที่เกี่ยวข้องกับตัว Directory เองคือ

    dir2
    .

---

# 3. 📁 Directory ที่มี Subdirectory

นี่คือจุดสำคัญที่สุด

โดยทั่วไปสามารถใช้สูตร

    Link Count = 2 + จำนวน Subdirectory

เหตุผลคือทุก Subdirectory จะมี `..` ซึ่งชี้กลับมายัง Parent Directory

ตัวอย่าง

    parent/
    ├── dir1/
    └── dir2/

ภายใน

    dir1/..
    
จะชี้กลับไปที่

    parent/

และ

    dir2/..

ก็ชี้กลับไปที่

    parent/

ดังนั้น Parent Directory จะมี Link เพิ่มขึ้นตามจำนวน Subdirectory

---

# 🔍 วิเคราะห์ Home Directory จริงของ User `itk`

บรรทัดนี้คือ Directory ปัจจุบัน

    drwx------. 4 itk itk 4096 Sep 18 11:51 .

ค่า Link Count คือ

    4

เราสามารถอธิบายได้ว่า

    Link Count = 2 + จำนวน Subdirectory

ดังนั้น

    4 = 2 + จำนวน Subdirectory

จึงได้

    จำนวน Subdirectory = 4 - 2
                        = 2

จาก `ls -la` เราพบ Directory ย่อย 2 ตัวคือ

    dir2
    .ssh

ดังนั้นจึงตรงกับ Link Count = 4

---

# 🔬 แกะ Link Count = 4 ทีละขั้น

Home Directory ของ User `itk` มีโครงสร้างโดยย่อดังนี้

    /home/itk/
    ├── dir2/
    └── .ssh/

ในกรณีนี้ Directory `/home/itk` มี

    1. `.` ของตัวเอง
    2. Entry จาก Parent Directory ที่ชี้มายัง `/home/itk`
    3. `..` จาก `dir2`
    4. `..` จาก `.ssh`

จึงได้

    1 + 1 + 1 + 1 = 4

หรือเขียนตามสูตร

    2 + จำนวน Subdirectory
    2 + 2
    = 4

ดังนั้น

    /home/itk
    Link Count = 4

---

# ⚠️ จุดที่มักเข้าใจผิด

บางครั้งจะคิดว่า

    tree

แสดง Directory ทั้งหมดที่มีอยู่

แต่จริง ๆ แล้ว `tree` แบบปกติไม่ได้แสดง Hidden Files และ Hidden Directories

ใน Linux ชื่อที่ขึ้นต้นด้วย `.` ถือเป็น Hidden Entry เช่น

    .ssh
    .bash_history
    .bashrc
    .lesshst

ดังนั้นผลลัพธ์

    tree

จึงแสดง

    .
    ├── before.txt
    ├── dir2
    │   └── index.html
    ├── multi-user.target-direct.txt
    ├── multi-user.target-full.txt
    ├── multi-user.target-sorted.txt
    ├── multi-user.target.txt
    └── prepare-template.sh

และสรุปว่า

    2 directories, 7 files

แต่ Directory ที่ `tree` นับรวมในที่นี้คือ

    .
    └── dir2

จึงได้ 2 directories

ส่วน `.ssh` ถูกซ่อนอยู่ จึงไม่แสดงใน `tree` แบบปกติ

---

# 👀 ถ้าต้องการให้ tree แสดง Hidden Items

สามารถใช้

    tree -a

จะเห็น Hidden Files และ Hidden Directories ด้วย

ตัวอย่างแนวคิด

    .
    ├── .bash_history
    ├── .bash_logout
    ├── .bash_profile
    ├── .bashrc
    ├── .lesshst
    ├── .ssh
    │   └── ...
    ├── before.txt
    ├── dir2
    │   └── index.html
    └── ...

---

# 🔍 ตรวจสอบ Link Count ของ Directory โดยตรง

ใช้

    ls -ld .

ผลลัพธ์จะประมาณ

    drwx------. 4 itk itk 4096 Sep 18 11:51 .

ตัวเลข `4` คือ Link Count ของ Home Directory

ตรวจสอบ `dir2`

    ls -ld dir2

จะได้ประมาณ

    drwxr-xr-x. 2 itk itk 24 Sep 17 14:04 dir2

ตัวเลข `2` คือ Link Count ของ `dir2`

ตรวจสอบ `.ssh`

    ls -ld .ssh

จะได้ประมาณ

    drwx------. 2 itk itk 71 Sep 17 14:55 .ssh

ตัวเลข `2` แสดงว่า `.ssh` ไม่มี Subdirectory อยู่ภายใน

---

# 🔗 ทดลองสร้าง Subdirectory

เข้าไปที่ `dir2`

    cd dir2

ตรวจสอบ

    ls -la

จะเห็น

    .
    ..
    index.html

ตอนนี้ Link Count ของ `dir2` คือ

    2

สร้าง Subdirectory เพิ่ม

    mkdir subdir

ตรวจสอบอีกครั้ง

    ls -ld .

จะเห็น Link Count เปลี่ยนเป็น

    3

เพราะตอนนี้มี

    .
    dir2
    subdir/..

หรือคิดด้วยสูตร

    Link Count = 2 + จำนวน Subdirectory

    = 2 + 1

    = 3

---

# 🔗 ถ้าสร้าง Subdirectory อีกตัว

    mkdir subdir2

ตรวจสอบ

    ls -ld .

จะได้ Link Count ประมาณ

    4

เพราะมี Subdirectory 2 ตัว

    Link Count = 2 + 2
               = 4

---

# 📄 Hard Link ของ Regular File

สำหรับ Regular File เราสามารถเพิ่ม Hard Link ได้ด้วยคำสั่ง

    ln index.html index_backup.html

ตรวจสอบ

    ls -li

ตัวอย่าง

    123456 -rw-r--r--. 2 itk itk 0 Sep 17 14:04 index.html
    123456 -rw-r--r--. 2 itk itk 0 Sep 18 14:00 index_backup.html

สิ่งสำคัญคือ inode number เหมือนกัน

    123456 → index.html
    123456 → index_backup.html

และ Link Count เป็น

    2

เพราะมี 2 Directory Entries ที่ชี้ไปยัง inode เดียวกัน

---

# 🧠 ภาพรวม Hard Link

    Directory Entry
          │
          ▼
        inode
          │
          ▼
       File Data

เมื่อสร้าง Hard Link

    index.html ─────────┐
                        │
                        ▼
                      inode
                        │
                        ▼
                     File Data
                        ▲
                        │
    index_backup.html ──┘

ดังนั้น

    index.html
    index_backup.html

ไม่ได้เป็นข้อมูล 2 ชุดแยกกัน

แต่เป็นชื่อ 2 ชื่อที่ชี้ไปยัง inode เดียวกัน

---

# 🔐 มุมมองสำหรับ SOC / Cybersecurity Admin

Hard Link มีความสำคัญในการตรวจสอบ Filesystem และ Threat Hunting เพราะชื่อไฟล์หลายชื่อสามารถชี้ไปยัง inode เดียวกันได้

ตรวจสอบ inode และ Link Count ด้วย

    ls -li

ค้นหา Regular File ที่มี Hard Link มากกว่า 1

    find /var -xdev -type f -links +1 -ls

สามารถใช้เพื่อค้นหาไฟล์ที่มีหลาย Directory Entries

ควรพิจารณาร่วมกับข้อมูลอื่น ๆ เช่น

    inode
    file ownership
    permissions
    timestamps
    SELinux context
    process activity
    audit logs

ไม่ควรสรุปว่าไฟล์ที่มี Hard Link หลายตัวเป็นภัยคุกคามโดยอัตโนมัติ เพราะ Hard Link เป็นความสามารถปกติของ Linux Filesystem

---

# 🎯 สรุปแบบจำง่าย

## 📄 Regular File

    File
      │
      └── Link Count เริ่มต้น = 1

ถ้าสร้าง Hard Link

    ln file1 file2

จะได้

    file1 ─────┐
               ├──> inode
    file2 ─────┘

    Link Count = 2

---

## 📁 Directory

Directory ที่ไม่มี Subdirectory

    Link Count = 2

โดยทั่วไปประกอบด้วย

    Parent Directory Entry
    .

ถ้ามี Subdirectory

    Link Count = 2 + จำนวน Subdirectory

ตัวอย่าง

    Directory
    ├── subdir1/
    └── subdir2/

จะมี

    Link Count = 2 + 2
               = 4

---

# 🧩 จากระบบของคุณ

Home Directory ของ `itk`

    /home/itk

มี Subdirectory สำคัญ 2 ตัว

    /home/itk/dir2
    /home/itk/.ssh

ดังนั้น

    Link Count = 2 + 2
               = 4

ตรงกับผลลัพธ์

    drwx------. 4 itk itk 4096 Sep 18 11:51 .

ส่วน `dir2` ไม่มี Subdirectory

    drwxr-xr-x. 2 itk itk 24 Sep 17 14:04 dir2

ดังนั้น

    Link Count = 2 + 0
               = 2

และ `before.txt` เป็น Regular File ที่มี Directory Entry เดียว

    -rw-r--r--. 1 itk itk 49152 Sep 17 17:31 before.txt

ดังนั้น

    Link Count = 1

---

# 🚀 จำ 3 ตัวเลขนี้ให้แม่น

    Regular File
    └── 1

    Directory ไม่มี Subdirectory
    └── 2

    Directory มี Subdirectory
    └── 2 + จำนวน Subdirectory

ตัวอย่างจากเครื่องจริง

    before.txt
    └── Link Count = 1

    dir2
    └── Link Count = 2

    /home/itk
    └── Link Count = 4
        ├── พื้นฐาน 2
        ├── dir2
        └── .ssh
