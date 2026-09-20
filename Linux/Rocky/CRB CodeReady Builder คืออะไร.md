# 🧩 CRB (CodeReady Builder) คืออะไร?
## Repository สำคัญบน Rocky Linux สำหรับ Development และ Dependency

ในระบบตระกูล **RHEL / Rocky Linux / AlmaLinux** เราจะพบ Repository หลายตัว เช่น

- 🔵 `BaseOS`
- 🟢 `AppStream`
- 🟠 `CRB`
- 🟣 `EPEL`

หนึ่งใน Repository ที่ผู้ดูแลระบบ Linux ควรรู้จักคือ

> 🔧 **CRB — CodeReady Builder**

บน **Rocky Linux 9 และ Rocky Linux 10** Repository นี้ใช้ชื่อว่า `crb` และโดยทั่วไปไม่ได้เปิดใช้งานเป็นค่าเริ่มต้น

---

# 1️⃣ CRB ย่อมาจากอะไร?

**CRB = CodeReady Builder**

เป็น Repository ที่มี Package โดยเฉพาะกลุ่มที่เกี่ยวข้องกับ

- 🛠️ Development
- 📦 Build Dependencies
- 📚 Development Libraries
- 🔨 การ Compile Software
- 🧱 การสร้าง RPM Package
- 🔗 Dependency ของ Software บางประเภท

พูดง่าย ๆ คือ

> 💡 **CRB เป็นแหล่ง Package ที่ช่วยเติม Dependency สำหรับงาน Development, Build และ Software บางประเภท**

---

# 2️⃣ เปรียบเทียบ BaseOS / AppStream / CRB

สามารถมองภาพรวมได้แบบนี้

    Rocky Linux
    │
    ├── BaseOS
    │   └── Core Operating System
    │
    ├── AppStream
    │   └── Applications / Runtime
    │
    └── CRB
        └── Development / Build Dependencies

---

## 🔵 BaseOS

เป็นส่วนพื้นฐานของ Operating System เช่น

- Kernel
- systemd
- bash
- Core Utilities
- Libraries พื้นฐาน
- Package สำคัญของระบบ

โดยทั่วไป Repository นี้เปิดใช้งานอยู่แล้ว

---

## 🟢 AppStream

ใช้สำหรับ Application และ Runtime ต่าง ๆ เช่น

- Python
- PHP
- Node.js
- Database-related packages
- Development tools
- Application components

---

## 🟠 CRB

เน้น Package ที่เกี่ยวข้องกับ

- Development Libraries
- `*-devel` Packages
- Build Dependencies
- Libraries สำหรับ Compile Software
- Dependency ที่ Software บางตัวต้องการ

ดังนั้น CRB จึงมักเกี่ยวข้องกับงาน

> 👨‍💻 Developer  
> 🔨 Software Build  
> 📦 RPM Packaging  
> 🛠️ System Administrator  
> 🐧 Linux Administrator

---

# 3️⃣ ทำไม CRB ถึงสำคัญ?

ปัญหาที่ Linux Administrator พบได้บ่อย เช่น

    No match for argument: xxx-devel

หรือ

    Error:
    Problem: package A requires package B

ทั้งที่ Package ที่ต้องการควรมีอยู่ใน Rocky Linux

สาเหตุหนึ่งอาจเป็นเพราะ

> 🔍 Package หรือ Dependency นั้นอยู่ใน CRB แต่ CRB ยังไม่ได้เปิดใช้งาน

---

# 4️⃣ CRB กับ EPEL เกี่ยวข้องกันอย่างไร?

จุดนี้สำคัญมากสำหรับ Linux Administrator

**EPEL = Extra Packages for Enterprise Linux**

เป็น Repository ที่ให้ Package เพิ่มเติมสำหรับ Enterprise Linux

Package บางตัวจาก EPEL อาจมี Dependency ที่อยู่ใน CRB

ดังนั้นในกรณีที่ใช้ EPEL จึงมักต้องเปิด CRB เพื่อให้ DNF สามารถ Resolve Dependency ได้ครบ

ภาพรวม:

    Rocky Linux
          │
          ├── BaseOS
          │
          ├── AppStream
          │
          ├── CRB
          │    │
          │    └── Development / Dependencies
          │
          └── EPEL
               │
               └── Additional Packages
                       │
                       └── อาจต้องใช้ Dependency จาก CRB

ดังนั้นจำง่าย ๆ:

    CRB ≠ EPEL

แต่สามารถมีความสัมพันธ์กันในเรื่อง Dependency ได้

---

# 5️⃣ ตรวจสอบว่า CRB เปิดอยู่หรือไม่

ใช้คำสั่ง:

    dnf repolist

หรือ

    dnf repolist --enabled

ค้นหาเฉพาะ CRB:

    dnf repolist | grep -i crb

ถ้าเปิดอยู่ อาจเห็นลักษณะประมาณ:

    crb    Rocky Linux 10 - CRB

---

# 6️⃣ วิธีเปิด CRB บน Rocky Linux 9 / 10

ใช้คำสั่ง:

    sudo dnf config-manager --set-enabled crb

จากนั้นตรวจสอบ:

    dnf repolist | grep -i crb

หากต้องการดู Repository ที่เปิดใช้งานทั้งหมด:

    dnf repolist --enabled

---

# 7️⃣ ดูรายละเอียด Repository

สามารถใช้:

    dnf repoinfo crb

หรือ:

    dnf repolist -v

เพื่อดูข้อมูล เช่น

- Repository ID
- Repository Name
- Base URL
- Enabled Status
- Package Information
- Repository Metadata

---

# 8️⃣ ค้นหา Package ที่ต้องการ

สามารถใช้:

    dnf search devel

หรือค้นหา Package เฉพาะ:

    dnf search libxml2-devel

ดูรายละเอียด Package:

    dnf info libxml2-devel

ข้อมูลที่แสดงอาจมี Repository เช่น:

    Repository : crb

แสดงว่า Package นั้นมาจาก CRB

---

# 9️⃣ CRB ไม่ได้หมายความว่าต้องติดตั้ง Package จาก CRB โดยตรงเสมอ

เมื่อเปิด CRB:

    sudo dnf config-manager --set-enabled crb

ไม่ได้หมายความว่าเราต้องระบุ CRB ทุกครั้งเวลาติดตั้ง Package

ตัวอย่าง:

    sudo dnf install nginx

DNF จะตรวจสอบ Repository ที่เปิดใช้งานอยู่ และทำ Dependency Resolution ให้อัตโนมัติ

ถ้า Dependency ที่ต้องการอยู่ใน CRB และ Repository เปิดใช้งานอยู่ DNF สามารถนำ Package นั้นมาใช้ในการแก้ Dependency ได้

---

# 🔟 CRB กับ PowerTools

ถ้าทำงานกับ Rocky Linux หลาย Version ต้องจำจุดนี้ให้ดี

## Rocky Linux 8

ใช้ Repository:

    PowerTools

Repository ID:

    powertools

---

## Rocky Linux 9

ใช้:

    CRB

Repository ID:

    crb

---

## Rocky Linux 10

ใช้:

    CRB

Repository ID:

    crb

---

## 🧠 จำง่าย ๆ

    Rocky Linux 8
         │
         └── PowerTools

    Rocky Linux 9
         │
         └── CRB

    Rocky Linux 10
         │
         └── CRB

ดังนั้นถ้าเจอเอกสารเก่าที่บอกว่า

    sudo dnf config-manager --set-enabled powertools

ต้องระวังว่าเป็นคำแนะนำสำหรับ Rocky Linux 8

สำหรับ Rocky Linux 9/10 ใช้:

    sudo dnf config-manager --set-enabled crb

---

# 1️⃣1️⃣ CRB ไม่ใช่ EPEL

สองตัวนี้มักถูกเข้าใจผิดว่าเป็น Repository เดียวกัน

แต่จริง ๆ แล้วแตกต่างกัน

## 🟠 CRB

เป็น Repository ใน Rocky/RHEL ecosystem

เน้น:

- Development
- Build
- Libraries
- Dependencies

---

## 🟣 EPEL

EPEL ย่อมาจาก:

    Extra Packages for Enterprise Linux

มีเป้าหมายเพื่อเพิ่ม Package ที่ไม่ได้อยู่ใน Repository หลักของ Enterprise Linux

ดังนั้น:

    CRB ≠ EPEL

แต่:

    EPEL
      │
      └── Package บางตัว
              │
              └── ต้องการ Dependency
                      │
                      └── CRB

---

# 1️⃣2️⃣ ตัวอย่างสถานการณ์จริง

สมมติเราต้องติดตั้ง Software:

    sudo dnf install some-package

แต่พบ:

    No match for argument: some-package

หรือพบ Dependency:

    requires: xxx-devel

ขั้นตอนที่ควรตรวจสอบ:

## Step 1 — ตรวจสอบ Repository

    dnf repolist

---

## Step 2 — ตรวจสอบ CRB

    dnf repolist | grep -i crb

---

## Step 3 — เปิด CRB

หากยังไม่ได้เปิด:

    sudo dnf config-manager --set-enabled crb

---

## Step 4 — Refresh Metadata

    sudo dnf clean all
    sudo dnf makecache

---

## Step 5 — ค้นหา Package

    dnf search xxx-devel

---

# 1️⃣3️⃣ ตรวจสอบ Repository Configuration

ดูไฟล์ Repository:

    ls -l /etc/yum.repos.d/

สามารถค้นหา CRB:

    grep -R "^\[crb\]" /etc/yum.repos.d/

หรือดูรายละเอียด:

    dnf repoinfo crb

---

# 1️⃣4️⃣ คำสั่งสำหรับจัดการ CRB

## 🔍 ตรวจสอบ

    dnf repolist

---

## 🔍 ตรวจสอบเฉพาะ CRB

    dnf repolist | grep -i crb

---

## 🟢 เปิด CRB

    sudo dnf config-manager --set-enabled crb

---

## 🔴 ปิด CRB

    sudo dnf config-manager --set-disabled crb

---

## 🔍 ดูรายละเอียด

    dnf repoinfo crb

---

# 1️⃣5️⃣ CRB กับงาน Cybersecurity / SOC Lab

ใน Lab ด้าน Cybersecurity อาจมี Software ที่ต้องใช้ Dependency เพิ่มเติม เช่น

- 🔍 Threat Hunting Tools
- 🛡️ Security Tools
- 📊 Monitoring Tools
- 🧪 Analysis Tools
- 🔨 Development Tools
- 📦 Software ที่ต้อง Compile จาก Source

โครงสร้างสามารถมองได้แบบนี้:

    Rocky Linux
          │
          ├── BaseOS
          │
          ├── AppStream
          │
          ├── CRB
          │
          ├── EPEL
          │
          └── Security Tools
                  │
                  ├── Monitoring
                  ├── Analysis
                  ├── Threat Hunting
                  └── Development

ดังนั้น CRB จึงเป็น Repository ที่ Linux Administrator ควรรู้จัก โดยเฉพาะเมื่อพบปัญหา Dependency

---

# 1️⃣6️⃣ สิ่งที่ต้องระวัง

ไม่ควรคิดว่า

    "เปิด CRB แล้วทุก Package จะอยู่ที่นี่"

เพราะ CRB เป็นเพียงหนึ่งในหลาย Repository

Package อาจมาจาก:

    BaseOS
    AppStream
    CRB
    EPEL
    Repository อื่น ๆ

ดังนั้นเวลาตรวจสอบ Package ควรดู Repository ด้วย:

    dnf info package-name

หรือ:

    dnf repoquery package-name

---

# 🧠 Mindmap จำ CRB

    CRB
    │
    ├── CodeReady Builder
    │
    ├── Rocky Linux 9 / 10
    │
    ├── Development
    │
    ├── Build Dependencies
    │
    ├── Development Libraries
    │
    ├── Package Dependencies
    │
    ├── ไม่ใช่ EPEL
    │
    └── มักเกี่ยวข้องกับ EPEL
          │
          └── Dependency บางส่วนอาจมาจาก CRB

---

# 🎯 สรุป

> 🧩 **CRB (CodeReady Builder) คือ Repository สำหรับ Rocky Linux ที่รวบรวม Package โดยเฉพาะกลุ่ม Development, Build Dependencies และ Libraries ที่ Software บางประเภทต้องใช้**

สิ่งที่ควรจำ:

    Rocky Linux 8
        ↓
    PowerTools

    Rocky Linux 9
        ↓
    CRB

    Rocky Linux 10
        ↓
    CRB

คำสั่งสำคัญ:

    # ตรวจสอบ Repository
    dnf repolist

    # ตรวจสอบ CRB
    dnf repolist | grep -i crb

    # เปิด CRB
    sudo dnf config-manager --set-enabled crb

    # ปิด CRB
    sudo dnf config-manager --set-disabled crb

    # ดูรายละเอียด CRB
    dnf repoinfo crb

---

## ⭐ จำประโยคเดียว

> **BaseOS = Core OS | AppStream = Applications/Runtime | CRB = Build & Development Dependencies | EPEL = Extra Packages**
