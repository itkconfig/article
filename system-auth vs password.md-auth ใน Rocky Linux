# 🔐 `/etc/pam.d/system-auth` vs `/etc/pam.d/password-auth`

ในระบบ **Rocky Linux / RHEL** การยืนยันตัวตนของผู้ใช้ส่วนหนึ่งถูกควบคุมผ่าน **PAM (Pluggable Authentication Modules)** ซึ่งไฟล์ที่พบและมีความสำคัญมากคือ

* 🔐 `/etc/pam.d/system-auth`
* 🌐 `/etc/pam.d/password-auth`

แม้ชื่อจะดูคล้ายกัน แต่มีบทบาทที่แตกต่างกันตาม **Authentication Stack** ที่ Service นำไปใช้งาน

---

## 🔐 1. `/etc/pam.d/system-auth`

ไฟล์นี้เป็น **PAM configuration stack สำหรับ Authentication ของระบบ** ที่ Service ต่าง ๆ สามารถเรียกใช้งานผ่าน PAM ได้

ตัวอย่างสถานการณ์ เช่น

* 🖥️ Login ที่หน้าเครื่อง
* 🔑 การตรวจสอบ Password
* 🛡️ `sudo`
* 👤 การเปลี่ยน Password
* 🖥️ Desktop Login เช่น GDM
* 🔐 Service อื่น ๆ ที่อ้างอิง PAM stack นี้

ตัวอย่างเช่น

```text
User
 │
 ▼
Login / GDM / sudo
 │
 ▼
PAM
 │
 ▼
system-auth
 │
 ├── pam_env
 ├── pam_faillock
 ├── pam_pwquality
 ├── pam_unix
 └── pam_deny
```

### 🎯 หน้าที่สำคัญ

`system-auth` สามารถกำหนด Policy ที่เกี่ยวข้องกับ Authentication เช่น

🔑 Password Authentication
🚫 Account Lockout
🧂 Password Hashing
🛡️ Authentication Failure
🔄 Password Change
👤 Account Validation

ดังนั้น หากมีการแก้ไข Authentication Policy ที่อยู่ใน Stack นี้ อาจส่งผลต่อหลาย Service ที่เรียกใช้ Stack ดังกล่าว

---

# 🌐 2. `/etc/pam.d/password-auth`

ไฟล์นี้เป็น PAM configuration stack ที่ Service ซึ่งต้องการ **Password Authentication** โดยเฉพาะ มักนำไปใช้งาน

ตัวอย่างที่สำคัญคือ

### 🔐 SSH

```text
Remote Client
      │
      │ SSH
      ▼
   sshd
      │
      ▼
     PAM
      │
      ▼
password-auth
      │
      ├── pam_faillock
      ├── pam_unix
      ├── pam_sss
      └── pam_deny
```

จึงมักเกี่ยวข้องกับการ Authentication ผ่าน Network เช่น

* 🌐 SSH
* 🔐 Remote Login
* 🛡️ Network Services ที่ใช้ PAM
* 👤 การตรวจสอบ Username / Password ผ่าน Service

---

# ⚠️ 3. จุดที่ต้องเข้าใจให้ถูกต้อง

คำอธิบายว่า

> `system-auth` = Local
> `password-auth` = Remote

ถือเป็น **แนวทางจำแบบง่าย ๆ** แต่ไม่ใช่กฎตายตัวของ PAM

❗ PAM ไม่ได้แบ่งไฟล์ตามหลักว่า

```text
system-auth     = Local
password-auth   = Remote
```

แต่ Service แต่ละตัวจะเป็นผู้กำหนดว่า **จะเรียก PAM stack ใด**

ตัวอย่างเช่น

```text
/etc/pam.d/sshd
       │
       └── password-auth
```

ขณะที่ Service อื่นอาจเรียก

```text
/etc/pam.d/login
       │
       └── system-auth
```

ดังนั้นวิธีคิดที่ถูกต้องกว่าคือ

> 🔑 **ดูว่า Service นั้นเรียก PAM stack ใด**

---

# 🔎 4. วิธีตรวจสอบว่า Service ใช้ไฟล์ไหน

สามารถดู PAM configuration ของ `sshd` ได้ด้วย

```bash
sudo cat /etc/pam.d/sshd
```

มักพบลักษณะประมาณนี้

```text
auth       substack     password-auth
account    required     pam_sepermit.so
account    required     pam_nologin.so
password   include      password-auth
session    include      password-auth
```

จุดสำคัญคือ

```text
password-auth
```

หมายความว่า `sshd` กำลังเรียกใช้ PAM stack จาก

```text
/etc/pam.d/password-auth
```

---

# 🖥️ 5. แล้ว Local Login ใช้ไฟล์อะไร?

สามารถตรวจสอบ Service ที่เกี่ยวข้องได้ เช่น

```bash
sudo cat /etc/pam.d/login
```

หรือ Desktop Environment เช่น GDM

```bash
sudo cat /etc/pam.d/gdm-password
```

จากนั้นดูว่ามีการเรียก

```text
system-auth
```

หรือ

```text
password-auth
```

หรือไม่

---

# 🧩 6. ความสัมพันธ์ของ PAM Stack

แนวคิดสำคัญคือ PAM มีหลายประเภทของการทำงาน

```text
                 PAM
                  │
       ┌──────────┼──────────┐
       │          │          │
      auth      account    session
       │          │          │
       ▼          ▼          ▼
 Authentication  Account    Session
                 Policy     Management
```

และ Configuration ของ Service อาจเรียก Stack อื่นต่อไป เช่น

```text
sshd
 │
 ▼
/etc/pam.d/sshd
 │
 ▼
password-auth
 │
 ├── pam_faillock.so
 ├── pam_unix.so
 ├── pam_sss.so
 └── pam_deny.so
```

---

# 🛡️ 7. ทำไม SOC / Security Admin ต้องสนใจ?

สำหรับงาน **SOC / Cybersecurity / Linux Security** PAM เป็นจุดที่สำคัญมาก เพราะสามารถใช้ตรวจสอบและควบคุม Authentication ได้

ตัวอย่าง Policy ที่เกี่ยวข้อง

### 🔐 Password Policy

```text
pam_pwquality.so
```

ใช้ควบคุมคุณภาพของ Password

### 🚫 Account Lockout

```text
pam_faillock.so
```

ใช้จัดการ Failed Authentication และ Account Lockout

### 👤 Local Account Authentication

```text
pam_unix.so
```

เกี่ยวข้องกับ Local Linux Account

### 🌐 Central Authentication

```text
pam_sss.so
```

มักเกี่ยวข้องกับ SSSD เช่น

```text
FreeIPA
Active Directory
LDAP
```

---

# 🔍 8. Threat Hunting

หากกำลังตรวจสอบเหตุการณ์ SSH Brute Force สามารถตรวจสอบทั้ง PAM Configuration และ Authentication Log ได้

### ตรวจสอบ PAM

```bash
sudo grep -nE 'pam_faillock|pam_unix|pam_sss' \
/etc/pam.d/system-auth \
/etc/pam.d/password-auth
```

### ตรวจสอบ SSH Authentication

```bash
sudo journalctl -u sshd --since today
```

หรือ

```bash
sudo grep -Ei 'failed|failure|invalid|accepted' \
/var/log/secure
```

สามารถเชื่อมโยงเป็นภาพรวมได้ดังนี้

```text
              🌐 SSH Attack
                   │
                   ▼
                 sshd
                   │
                   ▼
                  PAM
                   │
                   ▼
            password-auth
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
   pam_faillock  pam_unix   pam_sss
        │          │          │
        ▼          ▼          ▼
   Lockout      Local       FreeIPA/
               Account        LDAP
                   │
                   ▼
              📝 Logs
                   │
                   ▼
            Wazuh / Graylog
                   │
                   ▼
              🚨 SOC Alert
```

---

# ⚠️ 9. อย่าแก้ไฟล์ PAM แบบไม่ตรวจสอบ

PAM เป็นส่วนสำคัญของ Authentication System

การแก้ไขผิดพลาด เช่น

```text
auth required ...
account required ...
password required ...
session required ...
```

อาจทำให้เกิดปัญหา

🚨 Login ไม่ได้
🚨 SSH เข้าไม่ได้
🚨 `sudo` ใช้งานไม่ได้
🚨 Account ถูก Lock โดยไม่ตั้งใจ
🚨 Authentication Loop

ดังนั้นก่อนแก้ไขควร Backup Configuration

```bash
sudo cp -a /etc/pam.d/system-auth \
/etc/pam.d/system-auth.bak

sudo cp -a /etc/pam.d/password-auth \
/etc/pam.d/password-auth.bak
```

---

# 🧠 สรุปจำง่าย

```text
🔐 system-auth
      │
      ├── Authentication Stack
      ├── Local / System Services
      ├── Login
      ├── sudo
      └── Services ที่เรียกใช้ Stack นี้


🌐 password-auth
      │
      ├── Password Authentication Stack
      ├── sshd มักเรียกใช้
      ├── Remote Authentication
      └── Services ที่เรียกใช้ Stack นี้
```

แต่หลักการที่ **ถูกต้องที่สุด** คือ

> 🧠 **อย่าตัดสินจากชื่อไฟล์ว่า Local หรือ Remote — ให้ตรวจสอบ PAM configuration ของ Service ว่าเรียก Stack ใด**

เช่น

```text
/etc/pam.d/sshd
      │
      └── password-auth
             │
             └── Authentication Policy


/etc/pam.d/login
      │
      └── system-auth
             │
             └── Authentication Policy
```

ดังนั้นสำหรับงาน **Linux Security / SOC / Threat Hunting** ควรตรวจสอบเป็น Chain:

```text
Service
   ↓
PAM Service Configuration
   ↓
system-auth / password-auth
   ↓
PAM Modules
   ↓
Authentication Event
   ↓
Journal / /var/log/secure
   ↓
Wazuh / Graylog
   ↓
SOC Detection
```

🔐 **PAM = จุดควบคุมสำคัญของ Linux Authentication**

การเข้าใจว่า Service ใดเรียก PAM Stack ใด จะช่วยให้การทำ **Hardening, Incident Response และ Threat Hunting** แม่นยำกว่าการจำเพียงว่า `system-auth = Local` และ `password-auth = Remote`
