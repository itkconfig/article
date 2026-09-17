# 🔐 parted — SOC / Cybersecurity Admin
# Rocky Linux
# Man Section: 8

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 1️⃣ ดูคู่มือ

man 8 parted

# ตรวจสอบว่า parted อยู่ Section ไหน
man -f parted

# หรือ
whatis parted


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 2️⃣ ดู Disk และ Partition แบบ Read-Only
# ⭐ ใช้บ่อยสำหรับ SOC / Admin

sudo parted -l

# แสดง Disk ทั้งหมด
# ไม่แก้ไขข้อมูล


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 3️⃣ ตรวจสอบ Disk ลูกที่ต้องการ

sudo parted /dev/sda print

# ดู Partition Table ของ /dev/sda


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 4️⃣ ดูข้อมูลแบบละเอียด

sudo parted /dev/sda print free

# แสดง:
# - Partition
# - Filesystem
# - Start / End
# - Free Space

# มีประโยชน์ในการตรวจสอบพื้นที่ที่ไม่ได้ถูกใช้งาน


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 5️⃣ ตรวจสอบชนิด Partition Table

sudo parted /dev/sda print

# ตัวอย่าง:
# Partition Table: gpt
#
# หรือ
#
# Partition Table: msdos


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 6️⃣ SOC — ตรวจสอบ Disk ที่ระบบมองเห็น

sudo parted -l

# ใช้ร่วมกับ

lsblk -f
lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINTS
blkid

# เป้าหมาย:
# ตรวจสอบว่ามี Disk / Partition
# ที่ไม่สอดคล้องกับ Baseline หรือไม่


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 7️⃣ SOC — ตรวจสอบ Partition ที่ไม่ได้ Mount

lsblk

sudo parted -l

# ตัวอย่างสิ่งที่ควรตรวจสอบ:
#
# /dev/sdb
# /dev/sdb1
#
# แต่ไม่มี Mount Point
#
# อาจเป็น:
# - Disk สำรอง
# - Storage ใหม่
# - Partition ที่ยังไม่ได้ใช้งาน
# - Evidence ที่ต้องตรวจสอบเพิ่มเติม
#
# ⚠️ การพบ Partition ไม่ได้หมายความว่าเป็นภัยคุกคาม


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 8️⃣ SOC — ตรวจสอบ Free Space

sudo parted /dev/sda print free

# ใช้ตรวจสอบ:
# - Free Space
# - Partition Boundary
# - Partition ที่ไม่ได้ใช้งาน


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 9️⃣ SOC — ตรวจสอบ Filesystem

sudo parted -l

# ใช้ร่วมกับ

lsblk -f

# เพื่อดู:
# - Filesystem
# - UUID
# - Mount Point
#
# เช่น:
#
# xfs
# ext4
# vfat


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔟 SOC — ตรวจสอบ Mount Configuration

cat /etc/fstab

# แล้วเปรียบเทียบกับ

lsblk -f

# และ

findmnt

# เป้าหมาย:
# ตรวจสอบว่า Disk / Partition
# ถูก Mount ตาม Configuration หรือไม่


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 1️⃣1️⃣ ตรวจสอบ Partition แบบ Interactive

sudo parted /dev/sda

# จากนั้นใช้:

print

# แสดง Partition Table

print free

# แสดง Free Space

quit

# ออกจาก parted


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 1️⃣2️⃣ คำสั่งที่ควรระวังมาก ⚠️

sudo parted /dev/sda mkpart ...

sudo parted /dev/sda rm ...

sudo parted /dev/sda mklabel ...

# คำสั่งเหล่านี้สามารถ:
# - สร้าง Partition
# - ลบ Partition
# - เปลี่ยน Partition Table
#
# ❗ ไม่ควรใช้กับ Production / Evidence
# โดยไม่ตรวจสอบให้แน่ใจก่อน


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 SOC Investigation Workflow

# Step 1 — ดู Disk

lsblk

        ↓

# Step 2 — ตรวจสอบ Partition

sudo parted -l

        ↓

# Step 3 — ตรวจสอบ Filesystem

lsblk -f

        ↓

# Step 4 — ตรวจสอบ Mount

findmnt

        ↓

# Step 5 — ตรวจสอบ Configuration

cat /etc/fstab

        ↓

# Step 6 — เปรียบเทียบกับ Baseline

Expected Disk
       VS
Actual Disk

        ↓

# Step 7 — Investigate Anomaly


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 จำง่ายสำหรับ MAN

parted
  ↓
System Administration
  ↓
Section 8

man 8 parted

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 SOC Cheat Sheet

# ดู Disk ทั้งหมด
sudo parted -l

# ดู Partition ของ Disk
sudo parted /dev/sda print

# ดู Partition + Free Space
sudo parted /dev/sda print free

# ดู Filesystem
lsblk -f

# ดู Mount
findmnt

# ดู Mount Configuration
cat /etc/fstab


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔐 Security Perspective

parted
  │
  ├── Disk Inventory
  │
  ├── Partition Inventory
  │
  ├── Filesystem Verification
  │
  ├── Free Space Inspection
  │
  ├── Mount Verification
  │
  └── Baseline Comparison
          │
          ↓
      SOC / Threat Hunting

# ⭐ หลักการ

"ตรวจสอบก่อนแก้ไข"

Read-Only
    ↓
Inventory
    ↓
Verify
    ↓
Investigate
    ↓
Change

