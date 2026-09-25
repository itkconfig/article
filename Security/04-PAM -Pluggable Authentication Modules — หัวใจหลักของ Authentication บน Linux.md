# 🔐 PAM (Pluggable Authentication Modules) — หัวใจหลักของ Authentication บน Linux

**PAM (Pluggable Authentication Modules)** คือกลไกสำคัญที่ Linux ใช้ในการจัดการ **Authentication และ Access Control**

เพื่อให้เห็นภาพง่าย ๆ ให้ลองมองว่า PAM คือ

> 🛡️ **"บริษัท รปภ. ส่วนกลางของ Linux Server"**

เมื่อผู้ใช้พยายาม Login ผ่าน SSH, ใช้ `su`, `sudo` หรือ Login ผ่านระบบอื่น ๆ โปรแกรมเหล่านี้ไม่จำเป็นต้องเขียนระบบตรวจสอบรหัสผ่านขึ้นมาเองทั้งหมด แต่สามารถเรียกใช้ PAM เพื่อจัดการกระบวนการ Authentication และ Policy ที่เกี่ยวข้อง

---

# 🧠 PAM คืออะไร?

ในอดีตโปรแกรมแต่ละตัวอาจต้องจัดการ Authentication ด้วยตัวเอง เช่น

- SSH ต้องตรวจสอบ Password
- `su` ต้องตรวจสอบ Password
- `login` ต้องตรวจสอบ Password
- โปรแกรมอื่น ๆ ก็ต้องมีโค้ดสำหรับ Authentication ของตัวเอง

เมื่อมี PAM เข้ามา Application สามารถส่งงานด้าน Authentication ไปให้ PAM จัดการ

แนวคิดง่าย ๆ คือ

    User
      │
      │ Login / Authentication
      ▼
    Application
      │
      │ "PAM ช่วยตรวจสอบให้หน่อย"
      ▼
    ┌───────────────────────────────┐
    │             PAM               │
    │  Pluggable Authentication     │
    │  Modules                      │
    └───────────────────────────────┘
      │
      ├── Password
      ├── Account Policy
      ├── 2FA
      ├── Password Quality
      ├── Session Limits
      └── Security Policy
      │
      ▼
    Success / Denied

ดังนั้น Application จึงไม่จำเป็นต้องรู้รายละเอียดของ Authentication Method ทุกชนิดด้วยตัวเอง

---

# 🔌 ทำไมถึงเรียกว่า "Pluggable"?

คำว่า **Pluggable** หมายถึง สามารถนำ Module ต่าง ๆ มา **เสียบเพิ่ม เปลี่ยน หรือถอดออก** ได้ตามต้องการ

ตัวอย่างเช่น

- ต้องการ Password Authentication → ใช้ PAM Module สำหรับ Password
- ต้องการ 2FA → เพิ่ม PAM Module สำหรับ OTP/2FA
- ต้องการ Fingerprint → เพิ่ม Module สำหรับ Fingerprint
- ต้องการ Password Complexity → เพิ่ม Module สำหรับ Password Policy
- ต้องการ Account Lockout → เพิ่ม Module สำหรับ Failed Authentication

แนวคิดคือ

    PAM
     │
     ├── Password
     │
     ├── 2FA
     │
     ├── Fingerprint
     │
     ├── Password Policy
     │
     └── Account Lockout

ข้อดีคือ Application เช่น SSH ไม่จำเป็นต้องถูกแก้ Source Code ทุกครั้งที่ต้องการเปลี่ยน Authentication Policy

---

# 🔐 ตัวอย่าง Authentication Chain

ถ้าใช้ Password อย่างเดียว

    User
      │
      ▼
    Password
      │
      ▼
    PAM
      │
      ▼
    Success / Denied

ถ้าเพิ่ม 2FA

    User
      │
      ▼
    Password
      │
      ▼
    PAM
      │
      ▼
    2FA / OTP
      │
      ▼
    PAM
      │
      ▼
    Success / Denied

จึงสามารถสร้าง Authentication Chain ที่มีหลายขั้นตอนได้

---

# 📂 PAM Configuration อยู่ที่ไหน?

บน Linux ที่ใช้ PAM Configuration แบบแยก Service ไฟล์หลักจะอยู่ใน

    /etc/pam.d/

ตรวจสอบได้ด้วยคำสั่ง

    ls -la /etc/pam.d/

ตัวอย่างไฟล์ที่อาจพบ

    /etc/pam.d/
    ├── sshd
    ├── sudo
    ├── su
    ├── login
    ├── passwd
    ├── system-auth
    ├── password-auth
    └── ...

แต่ละ Service สามารถมี PAM Policy ของตัวเองได้

ตัวอย่างเช่น

    /etc/pam.d/sshd

ใช้กับ SSH

    /etc/pam.d/su

ใช้กับ `su`

    /etc/pam.d/sudo

ใช้กับ `sudo`

---

# 🧩 PAM มี 4 Management Groups หลัก

PAM Configuration แบ่งการทำงานหลักออกเป็น 4 กลุ่ม

    ┌───────────────────────────────┐
    │             PAM               │
    ├───────────────────────────────┤
    │ auth                          │
    │ account                       │
    │ password                      │
    │ session                       │
    └───────────────────────────────┘

จำง่าย ๆ

    auth
      = คุณคือใคร?

    account
      = บัญชีนี้สามารถใช้งานได้หรือไม่?

    password
      = จัดการ Password อย่างไร?

    session
      = จัดการ Session อย่างไร?

---

# 1️⃣ auth — Authentication

`auth` ย่อมาจาก **Authentication**

หน้าที่หลักคือ

> 🔍 "พิสูจน์ว่าผู้ใช้คือคนที่อ้างว่าเป็นจริงหรือไม่?"

ตัวอย่างเช่น

    Username
       │
       ▼
    Password
       │
       ▼
    PAM auth
       │
       ├── ถูกต้อง ──────► Success
       │
       └── ไม่ถูกต้อง ───► Denied

สิ่งที่อาจเกี่ยวข้องกับ `auth`

    Password
    OTP
    2FA
    Smart Card
    Fingerprint
    Authentication Token

ตัวอย่าง PAM Module ที่พบได้บ่อย

    pam_unix.so

ใช้สำหรับ Authentication ที่เกี่ยวข้องกับ Local Linux Account

---

# 2️⃣ account — Account Management

`account` ใช้ตรวจสอบว่า

> "แม้ Password ถูกต้อง แต่บัญชีนี้ได้รับอนุญาตให้ใช้งานหรือไม่?"

ตัวอย่าง Policy เช่น

    Password ถูกต้อง
          │
          ▼
    Account ตรวจสอบ
          │
          ├── Account หมดอายุ?
          ├── Account ถูก Lock?
          ├── Login ถูกจำกัด?
          └── Account Policy ผ่านหรือไม่?
          │
          ▼
      Allow / Deny

ตัวอย่างสถานการณ์

    Username: user01
    Password: ถูกต้อง

    แต่...

    Account Expired
          │
          ▼
        Denied

ดังนั้น

> **Authentication สำเร็จ ไม่ได้หมายความว่าจะได้รับอนุญาตให้ใช้งานเสมอไป**

นี่เป็นแนวคิดสำคัญของ Linux Security

---

# 3️⃣ password — Password Management

`password` ใช้สำหรับจัดการเกี่ยวกับ **Password**

เช่น

    Change Password
          │
          ▼
    PAM password
          │
          ├── Password Complexity
          ├── Password History
          ├── Password Length
          └── Password Policy
          │
          ▼
    New Password

ตัวอย่าง Module ที่เกี่ยวข้องกับ Password Policy

    pam_pwquality.so

ใช้ตรวจสอบคุณภาพและความซับซ้อนของ Password

และ

    pam_pwhistory.so

ใช้จัดการ Password History

ตัวอย่างการทำงาน

    User changes password
            │
            ▼
    pam_pwquality.so
            │
            ├── Length
            ├── Complexity
            └── Quality
            │
            ▼
    pam_pwhistory.so
            │
            ▼
    Check previous passwords
            │
            ▼
    New Password

ดังนั้นกฎเกี่ยวกับ

- Password Complexity
- Password History
- Password Length

สามารถเชื่อมโยงกับ PAM ได้

---

# 4️⃣ session — Session Management

`session` ใช้จัดการสิ่งที่เกิดขึ้นเมื่อผู้ใช้เริ่มต้นและสิ้นสุด Session

ตัวอย่างเช่น

    Login
      │
      ▼
    PAM session
      │
      ├── Prepare Environment
      ├── Apply Limits
      ├── Create Session
      └── Session Cleanup
      │
      ▼
    User Shell

ตัวอย่าง Module ที่สำคัญ

    pam_limits.so

Module นี้สามารถใช้ร่วมกับ

    /etc/security/limits.conf

หรือ

    /etc/security/limits.d/

เพื่อกำหนด Resource Limits ของ User

ตัวอย่างแนวคิด

    User
      │
      ▼
    PAM Session
      │
      ▼
    pam_limits.so
      │
      ├── Number of Processes
      ├── Open Files
      ├── Login Sessions
      └── Other Resource Limits

---

# 🗺️ ภาพรวม 4 ส่วนของ PAM

                          PAM
                        │
          ┌─────────────┼─────────────┐
          │             │             │
          ▼             ▼             ▼
        auth          account      password
          │             │             │
          │             │             │
          ▼             ▼             ▼
     "คุณคือใคร?"   "ใช้บัญชีได้ไหม?"  "จัดการ Password"
          │             │             │
          └─────────────┼─────────────┘
                        │
                        ▼
                     session
                        │
                        ▼
                 "จัดการ Session"

จำง่าย ๆ

    auth
    = คุณคือใคร?

    account
    = คุณมีสิทธิ์ใช้บัญชีหรือไม่?

    password
    = จัดการ Password

    session
    = จัดการ Session

---

# 🔗 PAM กับ Linux Security

เมื่อเราทำ Linux Hardening หลายอย่าง เรากำลังใช้ PAM เป็นส่วนหนึ่งของ Security Architecture

ตัวอย่าง

                      Linux Security
                             │
                             ▼
                            PAM
                             │
          ┌──────────────────┼──────────────────┐
          │                  │                  │
          ▼                  ▼                  ▼
    Authentication     Account Policy     Password Policy
          │                  │                  │
          ▼                  ▼                  ▼
       Password           Lockout          Complexity
       2FA                Expiration       History
       MFA                Access           Length
          │                  │                  │
          └──────────────────┼──────────────────┘
                             │
                             ▼
                          Session
                             │
                             ▼
                      Resource Limits

---

# 🔐 PAM กับ SSH

สมมติผู้ใช้ Login ผ่าน SSH

    ssh itk@192.168.1.13

แนวคิดการทำงานโดยย่อ

    SSH Client
        │
        ▼
       sshd
        │
        ▼
       PAM
        │
        ├── auth
        │     └── ตรวจสอบ Authentication
        │
        ├── account
        │     └── ตรวจสอบ Account Policy
        │
        ├── password
        │     └── ใช้เมื่อมีการเปลี่ยน Password
        │
        └── session
              └── สร้างและจัดการ Session
        │
        ▼
    Allow / Deny

ดังนั้น `sshd` สามารถใช้ PAM เป็น Framework ในการจัดการ Authentication และ Policy ที่เกี่ยวข้อง

---

# 🛡️ PAM กับ Account Lockout

อีกตัวอย่างหนึ่งคือการป้องกันการ Login ผิดหลายครั้ง

แนวคิด

    User Login
        │
        ▼
    Password Incorrect
        │
        ▼
       PAM
        │
        ▼
    Failed Attempts
        │
        ├── ยังไม่ถึง Threshold
        │          │
        │          ▼
        │       Try Again
        │
        └── ถึง Threshold
                   │
                   ▼
              Account Locked

บนระบบ Linux สมัยใหม่ อาจพบ Module ที่เกี่ยวข้องกับ Failed Authentication เช่น

    pam_faillock.so

ทั้งนี้รายละเอียด Configuration จะแตกต่างกันตาม Linux Distribution และ Authentication Stack ที่ระบบใช้งาน

---

# 🔎 วิธีตรวจสอบ PAM Configuration

เริ่มจากตรวจสอบ Directory

    ls -la /etc/pam.d/

ดู Configuration ของ SSH

    cat /etc/pam.d/sshd

ดู Configuration ของ `su`

    cat /etc/pam.d/su

ดู Configuration ของ `sudo`

    cat /etc/pam.d/sudo

ตรวจสอบ Authentication Stack ที่ระบบใช้งาน เช่น

    cat /etc/pam.d/system-auth

และ

    cat /etc/pam.d/password-auth

> ⚠️ ชื่อไฟล์และโครงสร้าง PAM อาจแตกต่างกันตาม Linux Distribution และวิธีที่ระบบจัดการ Authentication Configuration

---

# 🧠 PAM ไม่ใช่แค่ "ระบบตรวจ Password"

สิ่งสำคัญที่ควรเข้าใจคือ PAM ไม่ได้มีหน้าที่เพียงตรวจสอบ Password

PAM ครอบคลุม

    PAM
    │
    ├── Authentication
    │
    ├── Account Management
    │
    ├── Password Management
    │
    └── Session Management

ดังนั้น PAM จึงเป็น

> **Framework สำหรับเชื่อม Application เข้ากับ Authentication และ Security Policies**

---

# 🏗️ PAM ในมุมมอง Security Architecture

สามารถมอง Architecture ได้ดังนี้

                         User
                           │
                           ▼
                     Application
                           │
              ┌────────────┼────────────┐
              │            │            │
             ssh           su          sudo
              │            │            │
              └────────────┼────────────┘
                           │
                           ▼
                          PAM
                           │
              ┌────────────┼────────────┐
              │            │            │
              ▼            ▼            ▼
            auth         account      password
              │            │            │
              └────────────┼────────────┘
                           │
                           ▼
                        session
                           │
                           ▼
                     Linux System

ข้อดีคือ Application ไม่จำเป็นต้องรู้รายละเอียดของ Authentication Method ทุกชนิด

Application เพียงเรียกใช้ PAM แล้ว PAM จะจัดการ Module และ Policy ตาม Configuration

---

# 🔐 PAM กับ Centralized Security Policy

หนึ่งในประโยชน์สำคัญของ PAM คือช่วยให้ Security Policy สามารถถูกจัดการผ่าน Framework กลาง

ตัวอย่าง Password Policy

    Password Policy
           │
           ▼
         PAM
           │
           ├── Password Complexity
           ├── Password History
           └── Password Authentication

ตัวอย่าง Account Policy

    Account Policy
           │
           ▼
         PAM
           │
           ├── Account Expiration
           ├── Account Lock
           └── Access Restrictions

ตัวอย่าง Session Policy

    Session Policy
           │
           ▼
         PAM
           │
           ├── Resource Limits
           └── Session Controls

---

# ⚠️ ข้อควรระวังในการแก้ PAM

PAM เป็นส่วนสำคัญของ Authentication Stack

ดังนั้นการแก้ Configuration ผิดอาจทำให้

    Login ไม่ได้
        │
        ▼
    SSH เข้าไม่ได้
        │
        ▼
    sudo ใช้งานไม่ได้
        │
        ▼
    Administrator Lockout

ก่อนแก้ไข PAM ควร Backup Configuration

    sudo cp -a /etc/pam.d /etc/pam.d.backup

และควรเปิด Session สำหรับ Administrator สำรองไว้ก่อนทดสอบ Configuration ใหม่

โดยเฉพาะ Server ที่เข้าถึงผ่าน SSH

> ⚠️ หลีกเลี่ยงการแก้ PAM แบบทดลองแล้วปิด Session เดิมทันที เพราะหาก Configuration ผิด อาจทำให้ไม่สามารถ Login กลับเข้าระบบได้

---

# 🎯 สรุป PAM แบบจำง่าย

                    🔐 PAM
       Pluggable Authentication Modules
                         │
                         ▼
             "รปภ. ส่วนกลางของ Linux"
                         │
            ┌────────────┼────────────┐
            │            │            │
            ▼            ▼            ▼
          auth        account      password
            │            │            │
        คุณคือใคร?   ใช้บัญชีได้?   จัดการ Password
            │            │            │
            └────────────┼────────────┘
                         │
                         ▼
                      session
                         │
                         ▼
                  จัดการ Session

---

# 🧩 จำ 4 คำนี้ให้ได้

## 🔐 auth

**Authentication**

> "คุณคือใคร?"

## 👤 account

**Account Management**

> "บัญชีนี้ใช้งานได้หรือไม่?"

## 🔑 password

**Password Management**

> "จัดการ Password อย่างไร?"

## 🖥️ session

**Session Management**

> "เมื่อ Login แล้วจะจัดการ Session อย่างไร?"

---

# 🚀 บทสรุป

**PAM (Pluggable Authentication Modules)** คือหนึ่งในองค์ประกอบสำคัญของ Linux Security Architecture ที่ช่วยให้ Application ต่าง ๆ สามารถใช้ Authentication และ Security Policy ผ่าน Framework กลางได้

แนวคิดสำคัญคือ

    Application
         │
         ▼
        PAM
         │
         ├── auth
         ├── account
         ├── password
         └── session
         │
         ▼
    Security Policy
         │
         ▼
    Allow / Deny

ดังนั้นสิ่งที่เราทำเกี่ยวกับ

- 🔐 Password Policy
- 🔒 Account Lockout
- 👤 Account Restrictions
- 🔑 Authentication
- 🛡️ 2FA/MFA
- ⚙️ Session Management
- 📊 Resource Limits

หลายส่วนสามารถเชื่อมโยงเข้ากับ **PAM** ได้

จึงสามารถสรุปสั้น ๆ ได้ว่า

> **PAM คือ Framework กลางที่ช่วยให้ Linux สามารถนำ Authentication, Account Policy, Password Policy และ Session Policy มาประกอบกันเป็น Security Policy โดยไม่จำเป็นต้องแก้ไขโปรแกรมต้นทางทุกครั้งที่ต้องการเปลี่ยนวิธีควบคุมการเข้าถึง**

🔐 **เข้าใจ PAM = เข้าใจแกนสำคัญของ Linux Authentication และ Hardening**
