# 🔐 AAA: Authentication, Authorization & Accounting
## 3 หลักการสำคัญของการควบคุมสิทธิ์ใน Network & Cybersecurity

ในโลกของ **Network และ Cybersecurity** เรามักต้องตอบคำถามสำคัญ 3 ข้อก่อนอนุญาตให้ใครหรืออุปกรณ์ใดเข้าถึงระบบ

🔑 **คุณคือใคร?**  
🛡️ **คุณมีสิทธิ์ทำอะไร?**  
📊 **คุณทำอะไรไปบ้าง?**

ทั้ง 3 คำถามนี้คือแนวคิดของ **AAA**

> **AAA = Authentication + Authorization + Accounting**

AAA เป็นแนวคิดพื้นฐานของ **Access Control, Identity Management, Network Security และ Cybersecurity**

---

# 🔑 1. Authentication — "Who are you?"

**Authentication = การยืนยันตัวตน**

เป็นกระบวนการตรวจสอบว่า

> "คนหรืออุปกรณ์ที่กำลังขอเข้าใช้งาน คือใคร?"

ตัวอย่างกลไกที่ใช้ในการ Authentication ได้แก่

- 👤 Username / Password
- 📱 MFA / OTP / Push Notification
- 🔐 Digital Certificate
- 👆 Biometrics
- 🪪 Smart Card
- 🔑 SSH Key
- 🎫 Kerberos Ticket

ตัวอย่างการ Login เข้า Linux Server ผ่าน SSH

    User
      │
      ├── Username
      ├── Password / SSH Key
      └── MFA
           │
           ▼
    Authentication
           │
           ├── Identity Valid
           └── Identity Invalid

หากข้อมูลสามารถยืนยันตัวตนได้ ระบบจึงดำเนินการไปยังขั้นตอน **Authorization**

### 💡 จำง่าย ๆ

**Authentication = "คุณเป็นใคร?"**

---

# 🛡️ 2. Authorization — "What can you access?"

เมื่อระบบรู้แล้วว่า

> "คุณคือใคร?"

คำถามต่อไปคือ

> "แล้วคุณมีสิทธิ์ทำอะไรได้บ้าง?"

นี่คือหน้าที่ของ **Authorization**

ตัวอย่างเช่น

    User: itk

    สามารถ:
    ✓ Login Server
    ✓ Read Log
    ✓ Restart Nginx

    ไม่สามารถ:
    ✗ Delete User
    ✗ Change Firewall
    ✗ Access Root Shell

Authorization สามารถควบคุมได้หลายระดับ เช่น

- 👤 User Permissions
- 👥 Groups
- 🎭 Roles
- 🛡️ Security Policies
- 📁 File Permissions
- 🌐 Network Access
- ☁️ Cloud IAM Policies
- 🗄️ Database Permissions

---

# 🎭 ตัวอย่าง RBAC

**RBAC = Role-Based Access Control**

แนวคิดคือไม่ได้กำหนดสิทธิ์ให้ User ทุกคนโดยตรง แต่กำหนดผ่าน **Role**

    User
      │
      ▼
    Role
      │
      ├── SOC Analyst
      │      ├── Read Logs
      │      └── Investigate Alerts
      │
      ├── Linux Admin
      │      ├── Manage Services
      │      └── Manage Server
      │
      └── Security Admin
             ├── Manage Policies
             └── Manage Security Controls

ดังนั้น

**Authentication ≠ Authorization**

การ Login สำเร็จไม่ได้หมายความว่า User สามารถทำทุกอย่างได้

ตัวอย่าง:

    Authentication
          │
          ▼
    "นี่คือ User: itk"
          │
          ▼
    Authorization
          │
          ▼
    "User itk มีสิทธิ์อะไร?"

### 💡 จำง่าย ๆ

**Authorization = "คุณทำอะไรได้บ้าง?"**

---

# 📊 3. Accounting — "What did you do?"

เมื่อ User Login และได้รับสิทธิ์แล้ว ยังมีคำถามสำคัญอีกข้อหนึ่ง

> "แล้ว User คนนี้ทำอะไรไปบ้าง?"

นี่คือหน้าที่ของ **Accounting**

Accounting เกี่ยวข้องกับการเก็บข้อมูลกิจกรรมของ User และ Session เช่น

- 🕐 Login Time
- 🕐 Logout Time
- 👤 Username
- 🌐 Source IP Address
- 💻 Source Device
- 📁 Resource ที่เข้าถึง
- ⚙️ Command / Action
- ⏱️ Session Duration
- 📊 Resource Usage
- 🚨 Administrative Activity

ตัวอย่างข้อมูล Audit Log

    User       : admin
    Source IP  : 192.168.1.50
    Login Time : 09:15:22
    Server     : linux-server-01
    Action     : systemctl restart nginx
    Result     : Success
    Logout     : 09:32:10

ข้อมูลเหล่านี้มีประโยชน์สำหรับ

- 🔎 Incident Investigation
- 🕵️ Threat Hunting
- 📋 Compliance
- 📊 Security Monitoring
- 🚨 Incident Response
- 🧾 Audit

### 💡 จำง่าย ๆ

**Accounting = "คุณทำอะไรไปบ้าง?"**

---

# 🔄 AAA ทำงานร่วมกันอย่างไร?

สามารถมอง Flow ได้ดังนี้

    USER
      │
      ▼
    🔑 AUTHENTICATION
    "คุณคือใคร?"
      │
      ▼
    🛡️ AUTHORIZATION
    "คุณทำอะไรได้บ้าง?"
      │
      ▼
    🌐 RESOURCE
    Server / Application / Database
      │
      ▼
    📊 ACCOUNTING
    "คุณทำอะไรไปบ้าง?"
      │
      ▼
    📝 AUDIT LOG

ดังนั้น Flow หลักของ AAA คือ

**Identify → Allow → Record**

หรือ

**Authentication → Authorization → Accounting**

---

# 🌐 AAA ถูกนำไปใช้ที่ไหน?

AAA ไม่ได้จำกัดอยู่เฉพาะ Network Device เท่านั้น แต่สามารถพบได้ในระบบ Enterprise จำนวนมาก

## 🌐 Network Device

เช่น

    Router
    Switch
    Firewall
    Wireless Controller

## 🔐 VPN

ใช้ควบคุมว่า User คนใดสามารถเชื่อมต่อ VPN ได้ และสามารถบันทึก Session ของผู้ใช้งานได้

## 📡 Enterprise Wi-Fi

เช่น Network ที่ใช้

    802.1X
       │
       ▼
    RADIUS
       │
       ▼
    Authentication / Authorization / Accounting

## 🖥️ Server

เช่น

    Linux Server
    Windows Server
    Application Server

## ☁️ Cloud

ใช้แนวคิด IAM ในการควบคุม Identity และ Permission ของ Cloud Resources

## 📱 Application

กำหนดว่า User หรือ Role ใดสามารถเข้าถึง Function ต่าง ๆ ของ Application ได้

## 🗄️ Database

กำหนดสิทธิ์ เช่น

    SELECT
    INSERT
    UPDATE
    DELETE

---

# ⚙️ เทคโนโลยีที่เกี่ยวข้องกับ AAA

AAA เป็น **แนวคิด** ไม่ใช่ Software เพียงตัวเดียว

เทคโนโลยีต่าง ๆ สามารถเข้ามาช่วยทำหน้าที่ในแต่ละส่วนของ AAA ได้

---

# 🔵 RADIUS

**RADIUS = Remote Authentication Dial-In User Service**

มักพบใน

- Network Access
- Wi-Fi
- VPN
- 802.1X

ตัวอย่าง Flow

    User
      │
      ▼
    Switch / AP / VPN
      │
      ▼
    RADIUS Server
      │
      ├── Authentication
      ├── Authorization
      └── Accounting

ตัวอย่างเช่น Enterprise Wi-Fi

    Laptop
       │
       ▼
    Access Point
       │
       ▼
    RADIUS
       │
       ▼
    Identity Source

---

# 🟣 TACACS+

**TACACS+** มักพบในการบริหารจัดการ Network Device เช่น

    Router
    Switch
    Firewall

สามารถใช้ควบคุมสิทธิ์ของผู้ดูแลระบบ และสามารถรองรับการควบคุมคำสั่งและการบันทึกกิจกรรมของผู้ดูแลระบบได้

ตัวอย่างแนวคิด

    Network Admin
          │
          ▼
    Network Device
          │
          ▼
    TACACS+
          │
          ├── Authentication
          ├── Authorization
          └── Accounting

---

# 🟢 LDAP

**LDAP = Lightweight Directory Access Protocol**

LDAP เป็น Protocol สำหรับเข้าถึง Directory Service

Directory สามารถเก็บข้อมูล เช่น

    Users
    Groups
    Attributes

ระบบอื่นสามารถนำข้อมูลจาก Directory ไปใช้ประกอบการ Authentication และ Authorization ได้

---

# 🟦 Active Directory

ในสภาพแวดล้อม Windows Enterprise สามารถใช้ Active Directory สำหรับ

    User Identity
    Group
    Authentication
    Computer Account
    Group Policy
    Access Control

ตัวอย่างเช่น

    User
      │
      ▼
    Active Directory
      │
      ├── Identity
      ├── Group Membership
      └── Policy
             │
             ▼
          Access Control

---

# ☁️ IAM

ใน Cloud จะพบแนวคิด

**IAM = Identity and Access Management**

ตัวอย่างแนวคิด

    User
      │
      ▼
    Role / Policy
      │
      ├── Read
      ├── Write
      ├── Delete
      └── Admin

IAM ช่วยกำหนดว่า Identity ใดสามารถเข้าถึง Resource ใด และสามารถทำ Action อะไรได้บ้าง

---

# 🔐 AAA กับ Cybersecurity

AAA มีความสำคัญต่อ Cybersecurity เพราะช่วยสร้าง

**Identity + Access Control + Visibility + Accountability**

สามารถมองเป็น Flow ได้ดังนี้

    Authentication
          │
          ▼
    รู้ว่า "ใคร"
          │
          ▼
    Authorization
          │
          ▼
    รู้ว่า "ทำอะไรได้"
          │
          ▼
    Accounting
          │
          ▼
    รู้ว่า "ทำอะไรไป"

เมื่อเกิด Security Incident เราจึงสามารถนำข้อมูลเหล่านี้มาประกอบการตรวจสอบได้

ตัวอย่าง Timeline

    09:10  User login
    09:11  Access server
    09:13  Execute command
    09:15  Modify configuration
    09:16  Logout

ข้อมูลเหล่านี้สามารถนำไปเชื่อมต่อกับระบบต่าง ๆ เช่น

    Authentication Logs
           +
    Authorization / Access Logs
           +
    Accounting / Audit Logs
           +
    Endpoint Logs
           +
    Network Logs
           │
           ▼
       Security Monitoring
           │
           ▼
    Incident Investigation

---

# 🚨 ตัวอย่าง AAA ในงาน SOC

สมมติ SOC ได้รับ Alert

    User       : admin
    Source IP  : 192.168.1.52
    Action     : sudo
    Time       : 02:14

SOC Analyst สามารถนำแนวคิด AAA มาประกอบการตรวจสอบ

## 🔑 Authentication

คำถามคือ

> User นี้ Login เข้ามาจริงหรือไม่?

ตรวจสอบข้อมูล เช่น

    SSH Login
    VPN Login
    MFA
    Authentication Logs

## 🛡️ Authorization

คำถามคือ

> User นี้มีสิทธิ์ใช้ sudo หรือไม่?

ตรวจสอบข้อมูล เช่น

    User
    Group
    Role
    Sudo Policy
    Access Policy

## 📊 Accounting

คำถามคือ

> หลังจาก Login แล้ว User ทำอะไรต่อ?

ตรวจสอบข้อมูล เช่น

    Command History
    Audit Logs
    Session Logs
    Application Logs
    Network Logs

จากนั้นนำข้อมูลทั้งหมดมาสร้าง Timeline

    Authentication
          │
          ▼
    Authorization
          │
          ▼
    User Activity
          │
          ▼
    Accounting / Audit
          │
          ▼
    Incident Timeline

---

# 🧠 เทคนิคจำ AAA แบบง่ายที่สุด

จำเพียง 3 คำนี้

    🔑 Authentication
           ↓
        IDENTIFY
      "คุณคือใคร?"

           ↓

    🛡️ Authorization
           ↓
         ALLOW
      "ทำอะไรได้?"

           ↓

    📊 Accounting
           ↓
        RECORD
      "ทำอะไรไป?"

หรือจำเป็นภาษาไทยว่า

> 🔑 **Authentication = ระบุตัวตน**
>
> 🛡️ **Authorization = กำหนดสิทธิ์**
>
> 📊 **Accounting = บันทึกกิจกรรม**

---

# 🎯 สรุป AAA

    🔐 AAA

    ├── 🔑 Authentication
    │      └── Who are you?
    │
    ├── 🛡️ Authorization
    │      └── What can you access?
    │
    └── 📊 Accounting
           └── What did you do?

จำง่าย ๆ:

> 🔑 **Authentication → Identify**
>
> 🛡️ **Authorization → Allow**
>
> 📊 **Accounting → Record**

AAA จึงเป็นหนึ่งในแนวคิดพื้นฐานที่สำคัญของ

- 🔐 Network Security
- 🖥️ System Administration
- 🛡️ Cybersecurity
- 👤 Identity & Access Management
- 🚨 SOC
- 📊 SIEM
- 🌐 Network Administration

เพราะระบบที่ปลอดภัยไม่ได้มีเพียงการถามว่า

> **"ใครสามารถ Login ได้?"**

แต่ต้องตอบได้ด้วยว่า

> **"Login แล้วทำอะไรได้?"**

และที่สำคัญ

> **"หลังจากได้รับสิทธิ์แล้ว เขาทำอะไรไปบ้าง?"**

---

# 🔐 Secure Identity. Control Access. Track Activity.

**Authentication → Authorization → Accounting**

**Identify → Allow → Record**

#CyberSecurity #NetworkSecurity #AAA #Authentication #Authorization #Accounting #IAM #RADIUS #TACACS #LDAP #ActiveDirectory #SOC #SIEM #Linux #ITSecurity #NetworkAdmin #SecurityAdmin
