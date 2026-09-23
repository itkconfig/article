# 🔐 FreeIPA: IPA Admin Password vs Directory Manager Password

ในการติดตั้ง FreeIPA/IdM ระบบจะถาม Password สำคัญ 2 ชุด ได้แก่

- `IPA admin password`
- `Directory Manager password`

แม้ทั้งสองจะเป็น Password สำหรับผู้ดูแลระบบ แต่ **เป็นคนละบัญชีและอยู่คนละระดับของระบบ**

---

## 🟢 1. IPA Admin Password

บัญชีคือ

    admin

บัญชี `admin` เป็นผู้ดูแลระบบ **FreeIPA / Identity Management**

โดยใช้ Kerberos เป็นหลัก และเป็นบัญชีที่ Administrator จะใช้งานเป็นประจำในการบริหาร FreeIPA

ตัวอย่างการ Login

    kinit admin

จากนั้นตรวจสอบ Ticket

    klist

เมื่อ Authentication สำเร็จ สามารถใช้คำสั่ง `ipa` ได้ เช่น

    ipa user-find
    ipa user-add
    ipa group-find
    ipa host-find
    ipa service-find
    ipa sudorule-find
    ipa dnszone-find

### ตัวอย่างการสร้าง User

    ipa user-add somchai --first=Somchai --last=Admin

หรือค้นหา User

    ipa user-find

ดังนั้นให้จำง่าย ๆ ว่า

    admin
      ↓
    FreeIPA Administrator
      ↓
    Kerberos
      ↓
    IPA CLI / Web UI
      ↓
    Users / Groups / Hosts / Services / Sudo / DNS

---

# 🔴 2. Directory Manager Password

อีกบัญชีหนึ่งคือ

    Directory Manager

โดยทั่วไปมี Distinguished Name (DN) เป็น

    cn=Directory Manager

บัญชีนี้เป็น Administrator ระดับสูงของ **389 Directory Server (LDAP)** ซึ่งเป็น Directory Server ที่ FreeIPA ใช้งานเป็น Backend

โครงสร้างโดยประมาณคือ

    FreeIPA / IdM
        │
        └── 389 Directory Server
                │
                └── LDAP
                      │
                      └── cn=Directory Manager

Directory Manager จึงไม่ได้เป็น FreeIPA User แบบเดียวกับ `admin`

แต่เป็นบัญชีระดับ **LDAP Directory Server**

---

# 🆚 เปรียบเทียบสองบัญชี

| รายการ | IPA `admin` | Directory Manager |
|---|---|---|
| บัญชี | `admin` | `cn=Directory Manager` |
| ระบบที่ดูแล | FreeIPA / IdM | 389 Directory Server / LDAP |
| Authentication หลัก | Kerberos | LDAP |
| ใช้ `kinit admin` | ✅ | ❌ |
| ใช้ `ipa` CLI | ✅ | ❌ โดยตรง |
| จัดการ Users | ✅ | ระดับ LDAP |
| จัดการ Groups | ✅ | ระดับ LDAP |
| จัดการ Hosts | ✅ | ระดับ LDAP |
| จัดการ Services | ✅ | ระดับ LDAP |
| จัดการ Sudo Rules | ✅ | ไม่ใช่หน้าที่หลัก |
| จัดการ DNS ผ่าน IPA | ✅ | ไม่ใช่หน้าที่หลัก |
| ระดับ LDAP | ปกติผ่าน IPA | **สูงมาก / Directory Server ระดับสูงสุด** |
| ใช้งานประจำวัน | ✅ | ❌ |
| ควรใช้แทนกันหรือไม่ | ❌ | ❌ |

---

# 🧠 จำง่าย ๆ ด้วยแนวคิด 2 ชั้น

    ┌─────────────────────────────────────┐
    │            FreeIPA / IdM            │
    │                                     │
    │   ┌─────────────────────────────┐   │
    │   │      IPA Administrator      │   │
    │   │                             │   │
    │   │          admin              │   │
    │   │                             │   │
    │   │   Kerberos / IPA CLI / UI   │   │
    │   └─────────────────────────────┘   │
    │                  │                  │
    │                  ▼                  │
    │        Users / Groups / Hosts       │
    │        Services / Sudo / DNS        │
    │                                     │
    │   ┌─────────────────────────────┐   │
    │   │    389 Directory Server     │   │
    │   │                             │   │
    │   │   cn=Directory Manager      │   │
    │   │                             │   │
    │   │       LDAP / Directory      │   │
    │   └─────────────────────────────┘   │
    └─────────────────────────────────────┘

พูดง่าย ๆ คือ

    admin
      ↓
    "ผมเป็น Admin ของ FreeIPA"

    Directory Manager
      ↓
    "ผมเป็น Admin ของ LDAP Directory Server"

---

# 🔐 Directory Manager ใช้ตอนไหน?

ในงานปกติของ FreeIPA เราจะใช้

    admin

เป็นหลัก

แต่ Directory Manager จะมีประโยชน์เมื่อจำเป็นต้องทำงานระดับ LDAP/Directory Server โดยตรง เช่น

- ตรวจสอบ LDAP Database
- Troubleshooting 389 Directory Server
- งาน Maintenance ระดับ Directory Server
- งาน Replication ระดับ LDAP
- งานที่ต้องเข้าถึง LDAP โดยตรง
- กู้คืนหรือแก้ไขปัญหาระดับ Directory Server ในบางกรณี
- งาน Administration ที่ต้องใช้สิทธิ์ LDAP สูงมาก

ตัวอย่างแนวคิดของการเชื่อมต่อ LDAP โดยตรง

    ldapsearch -x \
      -H ldap://localhost \
      -D "cn=Directory Manager" \
      -W \
      -b "dc=example,dc=com"

คำสั่งนี้แตกต่างจากการใช้

    kinit admin

เพราะเป็นการ Authentication กับ LDAP โดยตรง

---

# ⚠️ ทำไมไม่ควรใช้ Directory Manager ในงานประจำ?

เพราะ Directory Manager มีสิทธิ์สูงมากในระดับ Directory Server

หากใช้ผิดคำสั่ง อาจกระทบโครงสร้าง LDAP ซึ่งเป็นส่วนสำคัญของ FreeIPA

ตัวอย่างแนวคิด

    FreeIPA
       │
       ├── Kerberos
       ├── LDAP
       ├── DNS
       ├── Certificates
       ├── Users
       ├── Groups
       ├── Hosts
       └── Services

LDAP เป็นส่วนสำคัญที่หลายองค์ประกอบของ FreeIPA พึ่งพา

ดังนั้นจึงควรใช้ Directory Manager เฉพาะเมื่อจำเป็นจริง ๆ

---

# 🔑 Password ทั้งสองตัวควรเหมือนกันไหม?

ไม่จำเป็นต้องเหมือนกัน

ตัวอย่างที่เหมาะสมกว่า

    IPA admin password
        ↓
    Password-A

    Directory Manager password
        ↓
    Password-B

โดย

    Password-A ≠ Password-B

เหตุผลคือเป็น Credential คนละชุด และมีวัตถุประสงค์ต่างกัน

หาก Credential ชุดหนึ่งถูกเปิดเผย ก็ไม่ควรทำให้อีกชุดหนึ่งถูกเปิดเผยตามไปด้วย

---

# 🧪 ตัวอย่างสถานการณ์จริง

## สถานการณ์ที่ 1: สร้าง User

ต้องการสร้าง User ชื่อ `somchai`

ใช้

    kinit admin

จากนั้น

    ipa user-add somchai --first=Somchai --last=Admin

กรณีนี้ใช้

    IPA admin

ไม่จำเป็นต้องใช้ Directory Manager

---

## สถานการณ์ที่ 2: ตรวจสอบ LDAP โดยตรง

ต้องการ Troubleshooting LDAP

อาจจำเป็นต้องใช้

    cn=Directory Manager

เพื่อ Authentication กับ LDAP โดยตรง

ตัวอย่าง

    ldapsearch -x \
      -H ldap://localhost \
      -D "cn=Directory Manager" \
      -W \
      -b "dc=example,dc=com"

กรณีนี้กำลังทำงานกับ

    LDAP / 389 Directory Server

ไม่ใช่การบริหาร FreeIPA ผ่าน `ipa` CLI

---

# 🧩 เปรียบเทียบให้เห็นภาพ

ลองนึกถึงระบบดังนี้

    FreeIPA = ระบบบริหารองค์กร

    admin
      ↓
    ผู้ดูแลระบบองค์กร
      ↓
    จัดการ User / Group / Host / Service / Sudo / DNS

    Directory Manager
      ↓
    ผู้ดูแลฐาน Directory ภายใน
      ↓
    จัดการ LDAP ระดับล่าง

ดังนั้น

    admin
    = Administrator ของ FreeIPA

    Directory Manager
    = Administrator ของ LDAP Directory Server

---

# 🚨 ข้อควรจำ

อย่าสับสนระหว่าง

    admin

กับ

    cn=Directory Manager

ทั้งสองเป็นคนละ Credential

และอย่าคิดว่า

    Directory Manager
    =
    FreeIPA admin

เพราะไม่ใช่บัญชีเดียวกัน

---

# 📌 Cheat Sheet

    ┌─────────────────────────────────────┐
    │             FreeIPA                 │
    └─────────────────────────────────────┘
                    │
          ┌─────────┴─────────┐
          │                   │
          ▼                   ▼
       admin          Directory Manager
          │                   │
          ▼                   ▼
     FreeIPA/IdM        389 Directory Server
          │                   │
       Kerberos              LDAP
          │                   │
          ▼                   ▼
    ipa command          LDAP operations
    Web UI               Low-level admin


จำสั้น ๆ:

    admin
    → ใช้บริหาร FreeIPA เป็นหลัก

    Directory Manager
    → ใช้บริหาร LDAP/389 Directory Server ระดับล่าง

    admin ≠ Directory Manager

    Password ของทั้งสองบัญชี
    → เป็นคนละ Password
