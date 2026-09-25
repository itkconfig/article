````markdown
# 🔐 FreeIPA / LDAP Structure — Mind Map

```text
dc=lab,dc=lan
(Base DN / จุดเริ่มต้นของโดเมน)
│
├── cn=accounts
│   (หมวดหมู่หลักสำหรับข้อมูลบุคคล อุปกรณ์ และกลุ่ม)
│   │
│   ├── cn=users
│   │   (ที่เก็บข้อมูลผู้ใช้งานระบบ)
│   │   ├── uid=admin
│   │   ├── uid=dev01
│   │   │   (บัญชีที่ทดสอบการ SSH และรันคำสั่ง tail)
│   │   └── uid=dev02
│   │       (บัญชีที่ถูกระงับ/Disable ไปก่อนหน้านี้)
│   │
│   ├── cn=groups
│   │   (ที่เก็บข้อมูลกลุ่มผู้ใช้งาน)
│   │   ├── cn=admins
│   │   ├── cn=ipausers
│   │   └── cn=trustadmins
│   │
│   ├── cn=computers
│   │   (ที่เก็บข้อมูลเครื่องเซิร์ฟเวอร์และไคลเอนต์)
│   │   ├── fqdn=ipa-master.lab.lan
│   │   │   (เครื่อง Master ของระบบ)
│   │   └── fqdn=lab-linux-node-01.lab.lan
│   │       (เครื่องเป้าหมายที่ตั้งสิทธิ์เข้าถึง)
│   │
│   └── cn=services
│       (ที่เก็บข้อมูล Service Principals)
│       └── krbprincipalname=host/lab-linux-node-01.lab.lan@LAB.LAN
│
├── cn=hbac
│   (Host-Based Access Control / นโยบายควบคุมการเข้าถึงโฮสต์)
│   │
│   ├── cn=hbacrules
│   │   └── cn=Dev01_Access_Node01
│   │       (กฎอนุญาตให้เข้าถึงเครื่อง)
│   │
│   └── cn=hbacservices
│       ├── cn=sshd
│       │   (อนุญาตให้รีโมท SSH)
│       ├── cn=sudo
│       │   (อนุญาตสิทธิ์ขอ sudo)
│       └── cn=su-l
│           (อนุญาตให้สลับ user)
│
├── cn=sudo
│   (Sudo Rules / นโยบายการยกระดับสิทธิ์คำสั่ง)
│   │
│   ├── cn=sudorules
│   │   └── cn=Sudo Allow Commands
│   │       (กฎอนุญาตคำสั่ง)
│   │
│   └── cn=sudocmds
│       └── cn=/usr/bin/tail
│           (อ้างอิงคำสั่งแบบ Absolute Path)
│
├── cn=kerberos
│   (การยืนยันตัวตนและการจัดการรหัสผ่าน)
│   │
│   └── cn=LAB.LAN
│       └── cn=global_policy
│           (Password Policy: อายุ 90 วัน, 14 อักขระ)
│
└── cn=sysaccounts
    (บัญชีระบบภายในสำหรับ System Bindings)
    │
    └── cn=dns
        (บัญชีสำหรับจัดการโซน DNS ภายใน)
````

## 🧠 สรุปภาพรวม

```text
                    ┌─────────────────────┐
                    │   dc=lab,dc=lan     │
                    │      Base DN        │
                    └──────────┬──────────┘
                               │
        ┌──────────────────────┼─────────────────────────┐
        │                      │                         │
        ▼                      ▼                         ▼
 ┌──────────────┐      ┌──────────────┐        ┌──────────────┐
 │  cn=accounts │      │   cn=hbac    │        │   cn=sudo    │
 │ Users/Groups │      │ Host Access  │        │ Sudo Rules   │
 │ Computers    │      │    Control   │        │              │
 │ Services     │      └──────────────┘        └──────────────┘
 └──────────────┘
        │
        ├── users
        │   ├── admin
        │   ├── dev01
        │   └── dev02
        │
        ├── groups
        │   ├── admins
        │   ├── ipausers
        │   └── trustadmins
        │
        ├── computers
        │   ├── ipa-master
        │   └── lab-linux-node-01
        │
        └── services
            └── host/lab-linux-node-01

        ┌──────────────────────┐
        │    cn=kerberos       │
        │ Authentication /     │
        │ Password Policy      │
        └──────────┬───────────┘
                   │
                   └── LAB.LAN
                       └── global_policy

        ┌──────────────────────┐
        │   cn=sysaccounts     │
        │ System Bindings      │
        └──────────┬───────────┘
                   │
                   └── cn=dns
```

## 🔐 ความสัมพันธ์ด้าน Security

```text
                    FreeIPA
                       │
          ┌────────────┼────────────┐
          │            │            │
          ▼            ▼            ▼
   Authentication  Authorization  Accounting
          │            │
          │            ├── HBAC
          │            │
          │            ├── Sudo Rules
          │            │
          │            └── Groups
          │
          └── Kerberos
                │
                └── Password Policy


User: dev01
     │
     ▼
Kerberos Authentication
     │
     ▼
HBAC
     │
     ├── Host: lab-linux-node-01
     │
     └── Service: sshd
     │
     ▼
SSH Access
     │
     ▼
Sudo Rule
     │
     └── /usr/bin/tail
     │
     ▼
Command Execution
```

## 🎯 Lab Flow

```text
dev01
  │
  │ kinit / SSH
  ▼
FreeIPA / Kerberos
  │
  ▼
HBAC Rule
  │
  ├── Host
  │     └── lab-linux-node-01
  │
  └── Service
        └── sshd
  │
  ▼
SSH Login
  │
  ▼
Sudo Policy
  │
  └── Sudo Allow Commands
        │
        └── /usr/bin/tail
  │
  ▼
Command Execution
  │
  ▼
Auditd / Wazuh
  │
  ▼
Security Monitoring
```

```
```
คำอธิบายเพิ่มเติม:

dc (Domain Component): คือการแบ่งชื่อโดเมนออกเป็นส่วนๆ เช่น lab.lan จะถูกแปลงเป็น dc=lab,dc=lan

cn (Common Name): ใช้เรียกชื่อโฟลเดอร์หลัก กลุ่ม หรือกฎต่างๆ เช่น cn=users, cn=admins

uid (User ID): ใช้สำหรับระบุชื่อล็อกอินของผู้ใช้งาน

fqdn (Fully Qualified Domain Name): ใช้ระบุชื่อเต็มของเครื่องคอมพิวเตอร์ในระบบ
