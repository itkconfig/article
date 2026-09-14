# 🔐 PAM คืออะไรใน Linux?

**PAM = Pluggable Authentication Modules**

PAM คือระบบกลางสำหรับจัดการ **Authentication และ Access Control** ของ Linux

ทำให้โปรแกรมต่าง ๆ สามารถใช้กลไกการตรวจสอบตัวตนร่วมกันได้ โดยไม่จำเป็นต้องเขียนระบบ Authentication ขึ้นมาใหม่เอง

---

## 🧠 PAM ทำหน้าที่อะไร?

มองง่าย ๆ ว่า PAM เป็น **ระบบรักษาความปลอดภัยตรงกลาง** ระหว่าง Application กับระบบ Authentication ของ Linux

```text
                 Application
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
        SSH         sudo       login
          │          │          │
          └──────────┼──────────┘
                     ▼
                   PAM
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
   Password      Account       Session
 Authentication   Check         Setup
```

---

# 🔑 ตัวอย่างการ Login ผ่าน SSH

เมื่อผู้ใช้สั่ง:

```bash
ssh itk@192.168.1.5
```

กระบวนการโดยย่อ:

```text
SSH
 │
 ▼
PAM
 │
 ├── ตรวจสอบ Password
 ├── ตรวจสอบ Account
 ├── ตรวจสอบ Policy
 ├── ตรวจสอบสิทธิ์
 └── สร้าง Session
 │
 ▼
Login สำเร็จ / ถูกปฏิเสธ
```

ดังนั้น PAM มีส่วนสำคัญในการตัดสินว่า

> **ผู้ใช้คนนี้สามารถเข้าสู่ระบบได้หรือไม่**

---

# 📁 PAM Configuration

บน Rocky Linux / RHEL Configuration ของ PAM อยู่ที่:

```bash
/etc/pam.d/
```

ตรวจสอบไฟล์ได้ด้วย:

```bash
ls -l /etc/pam.d/
```

อาจพบไฟล์ เช่น:

```text
sshd
sudo
login
su
password-auth
system-auth
```

ตัวอย่างการดู Configuration ของ SSH:

```bash
cat /etc/pam.d/sshd
```

---

#
