# 🐧 RHCSA vs RHCE ต่างกันอย่างไร?

**RHCSA** และ **RHCE** เป็น Certification ของ Red Hat ที่เกี่ยวข้องกับการบริหารระบบ Linux แต่มีความแตกต่างกันในด้าน **ระดับความสามารถและขอบเขตของงาน**

---

## 🐧 RHCSA — Red Hat Certified System Administrator

RHCSA เป็นระดับ **System Administrator**

เน้นทักษะพื้นฐานและทักษะสำคัญในการดูแลระบบ Linux / RHEL เช่น

* 👤 User / Group Management
* 🔐 File Permission / ACL / sudo
* 💽 Partition / LVM / Filesystem
* 📦 Package Management
* ⚙️ systemd / Services
* 🌐 Network Configuration
* 🔥 Firewall / SELinux
* 📝 Log และ Troubleshooting
* 💾 Mount / `/etc/fstab`
* ⏰ Cron / Scheduled Jobs
* 🔑 SSH

### 🎯 แนวคิดของ RHCSA

> **"ดูแล Linux Server ให้สามารถทำงานได้อย่างถูกต้องและปลอดภัย"**

---

## 🚀 RHCE — Red Hat Certified Engineer

RHCE เป็นระดับที่ **สูงกว่า RHCSA**

เน้นการบริหารระบบ Linux ในระดับที่ซับซ้อนขึ้น โดยเฉพาะเรื่อง

### 🤖 Automation

เครื่องมือสำคัญคือ **Ansible**

ตัวอย่างเช่น แทนที่จะ SSH เข้า Server ทีละเครื่องเพื่อทำงาน:

```text
Server 01
Server 02
Server 03
Server 04
Server 05
```

สามารถใช้ Ansible ทำงานพร้อมกันได้:

```text
                 Ansible
                    │
          ┌─────────┼─────────┐
          │         │         │
       Server 01 Server 02 Server 03
          │         │         │
          └─────────┼─────────┘
                    │
              Configuration
               Management
```

ตัวอย่างงานที่สามารถทำ Automation ได้:

* 📦 Install Package
* ⚙️ Configure Services
* 🔑 Configure SSH
* 🔥 Configure Firewall
* 👤 Create Users
* 📝 Deploy Configuration
* 🚀 Deploy Applications
* 🔐 Apply Security Policies

### 🎯 แนวคิดของ RHCE

> **"ใช้ Automation เพื่อบริหาร Linux Server จำนวนมากอย่างมีประสิทธิภาพ"**

---

# 📊 RHCSA vs RHCE

| หัวข้อ               | RHCSA                   | RHCE                       |
| -------------------- | ----------------------- | -------------------------- |
| ระดับ                | 🟢 System Administrator | 🔵 Engineer                |
| Linux Administration | ⭐⭐⭐⭐⭐                   | ⭐⭐⭐⭐⭐                      |
| Troubleshooting      | ⭐⭐⭐⭐                    | ⭐⭐⭐⭐                       |
| Security             | ⭐⭐⭐⭐                    | ⭐⭐⭐⭐                       |
| Automation           | ⭐⭐                      | ⭐⭐⭐⭐⭐                      |
| Ansible              | พื้นฐาน                 | **หัวใจสำคัญ**             |
| จำนวน Server         | 1 ถึงหลายเครื่อง        | **หลายเครื่อง / จำนวนมาก** |
| แนวคิด               | Manage Linux            | **Automate Linux**         |

---

# 🧠 มองเป็นเส้นทางการเรียน

```text
                 Linux Career
                      │
             ┌────────┴────────┐
             │                 │
          RHCSA              RHCE
             │                 │
     Linux Administration    Automation
             │                 │
       ┌─────┼─────┐           │
       │     │     │           │
      LVM  SELinux SSH       Ansible
       │     │     │           │
    Network systemd Firewall   │
       └─────┴─────┘           │
                               ▼
                     Manage Multiple
                       Linux Servers
```

---

# 🎯 สำหรับสายงาน Linux / Security

สามารถมองภาพได้ง่าย ๆ ว่า

### 🐧 RHCSA

```text
Linux Server
     │
     ├── Users
     ├── Permissions
     ├── Storage
     ├── Network
     ├── Services
     ├── Firewall
     ├── SELinux
     └── Troubleshooting
```

คือ **พื้นฐานที่ต้องรู้ในการดูแล Linux Server**

### 🚀 RHCE

```text
             Ansible
                │
      ┌─────────┼─────────┐
      ▼         ▼         ▼
   Linux 01   Linux 02   Linux 03
      │         │         │
      └─────────┼─────────┘
                ▼
        Automated Management
```

คือการนำความรู้ Linux จาก RHCSA มาต่อยอดด้วย **Automation**

---

# 🔥 สรุปสั้นที่สุด

> 🐧 **RHCSA = "ฉันดูแล Linux Server ได้"**

> 🚀 **RHCE = "ฉันใช้ Automation จัดการ Linux Server จำนวนมากได้"**

ดังนั้นเส้นทางที่เหมาะสมคือ:

```text
Linux Fundamentals
        ↓
     RHCSA
        ↓
 Linux Administration
        ↓
     Ansible
        ↓
      RHCE
        ↓
Automation / DevOps / Security
```

