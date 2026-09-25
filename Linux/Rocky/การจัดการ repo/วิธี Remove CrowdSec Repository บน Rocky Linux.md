# 🗑️ วิธี Remove CrowdSec Repository บน Rocky Linux

หากเคยเพิ่ม Repository ของ CrowdSec ด้วยคำสั่ง:

    curl -s https://packagecloud.io/install/repositories/crowdsec/crowdsec/script.rpm.sh | sudo bash

และต้องการลบเฉพาะ Repository ออก สามารถทำตามขั้นตอนด้านล่างได้

---

## 1. 🔍 ตรวจสอบ Repository ของ CrowdSec

ตรวจสอบก่อนว่า Repository ถูกเพิ่มเข้ามาด้วยชื่ออะไร:

    sudo dnf repolist all | grep -i crowdsec

ตัวอย่างผลลัพธ์:

    crowdsec_crowdsec    CrowdSec    enabled

---

## 2. 🗑️ ลบไฟล์ Repository

โดยทั่วไป Repository ที่ติดตั้งจาก PackageCloud จะสร้างไฟล์ไว้ใน:

    /etc/yum.repos.d/

สามารถลบไฟล์ Repository ของ CrowdSec ได้ด้วย:

    sudo rm -f /etc/yum.repos.d/crowdsec_crowdsec.repo

---

## 3. 🔄 ตรวจสอบว่า Repository ถูกลบแล้ว

รัน:

    sudo dnf repolist all | grep -i crowdsec

ถ้าไม่มี Output แสดงว่า Repository ของ CrowdSec ถูกลบออกแล้ว

---

## 4. 🔎 ตรวจสอบไฟล์ Repository ที่อาจเหลืออยู่

สามารถค้นหาไฟล์ที่เกี่ยวข้องกับ CrowdSec ได้ด้วย:

    sudo grep -Ril "packagecloud.io.*crowdsec" /etc/yum.repos.d/

ถ้าไม่มี Output แสดงว่าไม่มีไฟล์ Repository ที่อ้างถึง CrowdSec เหลืออยู่ใน `/etc/yum.repos.d/`

---

# ⚠️ สำคัญ: Remove Repo ≠ Remove CrowdSec

การลบ Repository จะลบเฉพาะแหล่ง Package ที่ DNF ใช้ดาวน์โหลด Software เท่านั้น

ไม่ได้ลบโปรแกรม CrowdSec ที่ติดตั้งอยู่แล้ว

ภาพรวม:

    PackageCloud
        │
        │ Repository
        ▼
    /etc/yum.repos.d/
        │
        └── crowdsec_crowdsec.repo
                  │
                  ▼
             DNF Repository
                  │
                  ▼
             CrowdSec Package

การลบ:

    sudo rm -f /etc/yum.repos.d/crowdsec_crowdsec.repo

จะได้:

    PackageCloud
        │
        X
        │
        ▼
    Repository ถูกลบ

แต่:

    CrowdSec Package
        │
        └── ยังคงติดตั้งอยู่

---

# 🧹 หากต้องการตรวจสอบว่า CrowdSec ยังติดตั้งอยู่หรือไม่

ใช้:

    rpm -qa | grep -i crowdsec

หรือ:

    dnf list installed | grep -i crowdsec

ตัวอย่าง:

    crowdsec.x86_64
    crowdsec-firewall-bouncer.x86_64

---

# 🎯 สรุปคำสั่ง

ตรวจสอบ Repo:

    sudo dnf repolist all | grep -i crowdsec

ลบ Repo:

    sudo rm -f /etc/yum.repos.d/crowdsec_crowdsec.repo

ตรวจสอบอีกครั้ง:

    sudo dnf repolist all | grep -i crowdsec

ค้นหา Repo ที่เกี่ยวข้อง:

    sudo grep -Ril "packagecloud.io.*crowdsec" /etc/yum.repos.d/

ตรวจสอบ Package ที่ติดตั้ง:

    rpm -qa | grep -i crowdsec

---

## 🧠 จำง่าย

    🔍 ตรวจสอบ Repo
       ↓
    sudo dnf repolist all | grep -i crowdsec
       ↓
    🗑️ ลบ Repo
       ↓
    sudo rm -f /etc/yum.repos.d/crowdsec_crowdsec.repo
       ↓
    🔍 ตรวจสอบอีกครั้ง
       ↓
    sudo dnf repolist all | grep -i crowdsec

> 💡 **หมายเหตุ:** หากต้องการลบ CrowdSec ออกจาก Rocky Linux ทั้งหมด ต้องจัดการเพิ่มเติมทั้ง Package, Service, Configuration และ Firewall Bouncer ไม่ใช่เพียงลบ Repository
