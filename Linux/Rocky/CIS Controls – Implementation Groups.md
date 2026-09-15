# 🛡️ CIS Controls – Implementation Groups

ตัวอักษร **IG 1**, **IG 2**, **IG 3** ย่อมาจาก **Implementation Groups** ภายใต้กรอบมาตรฐานความปลอดภัย **CIS Controls** เช่น CIS Controls v7 และ v8

Implementation Groups ใช้สำหรับแบ่งระดับ **ความเข้มงวดและความพร้อมขององค์กรในการนำมาตรการด้าน Cybersecurity ไปใช้งาน** โดยพิจารณาจากขนาดองค์กร ความซับซ้อนของระบบ และระดับความเสี่ยง

---

## 📌 IG 1 — Implementation Group 1

### 🧹 Essential Cyber Hygiene

**IG 1** คือระดับพื้นฐานที่เรียกว่า **Essential Cyber Hygiene** เหมาะสำหรับองค์กรที่ต้องการสร้างพื้นฐานด้าน Cybersecurity

**เหมาะสำหรับ**

* 🏢 ธุรกิจขนาดเล็กถึงขนาดกลาง
* 💻 ระบบไอทีทั่วไป
* 👥 องค์กรที่มีบุคลากรหรือทรัพยากรด้าน Security จำกัด

**เน้นการป้องกันภัยพื้นฐาน เช่น**

* 🔑 การจัดการรหัสผ่าน
* 🔄 การอัปเดตและติดตั้ง Security Patches
* 🖥️ การจัดการอุปกรณ์และ Asset
* 🔒 การควบคุมสิทธิ์การเข้าถึง
* 🌐 การปิด Port และ Service ที่ไม่จำเป็น
* 🛡️ การติดตั้งและใช้งานระบบป้องกัน Malware

> ⭐ **แนวคิดสำคัญ:** ทุกองค์กรควรเริ่มต้นจาก IG 1 และทำมาตรการพื้นฐานให้ครบถ้วนก่อนยกระดับไปยัง IG 2 และ IG 3

---

## 📌 IG 2 — Implementation Group 2

### 🏢 Advanced Security Practices

**IG 2** เหมาะสำหรับองค์กรที่มีระบบไอทีและความเสี่ยงที่ซับซ้อนมากขึ้น

**เหมาะสำหรับ**

* 🏢 องค์กรขนาดกลางถึงขนาดใหญ่
* 📊 องค์กรที่มีข้อมูลสำคัญ
* 🌐 ระบบ IT ที่มีความซับซ้อน
* 👨‍💻 องค์กรที่มีทีม IT หรือ Security โดยเฉพาะ

**มาตรการเพิ่มเติม เช่น**

* 🔐 การจัดการสิทธิ์ผู้ใช้งานอย่างเข้มงวด
* 📋 การจัดการ Asset และ Software อย่างเป็นระบบ
* 📝 การรวบรวมและจัดเก็บ **Log**
* 🔎 การตรวจสอบและวิเคราะห์ Security Events
* 🚨 การตรวจจับและตอบสนองต่อเหตุการณ์ด้านความปลอดภัย
* 🛡️ การเพิ่มมาตรการป้องกัน Endpoint และ Network

> 💡 **IG 2 = IG 1 + มาตรการ Security ที่มีความเข้มข้นและเป็นระบบมากขึ้น**

---

## 📌 IG 3 — Implementation Group 3

### 🚨 Advanced / High-Risk Security

**IG 3** เป็นระดับที่มีมาตรการด้าน Cybersecurity เข้มข้นที่สุด เหมาะสำหรับองค์กรที่มีความเสี่ยงสูงและอาจตกเป็นเป้าหมายของการโจมตีที่ซับซ้อน

**เหมาะสำหรับ**

* 🏦 สถาบันการเงินและธนาคาร
* 🏥 องค์กรที่มีข้อมูลสำคัญระดับสูง
* 🏛️ หน่วยงานภาครัฐ
* ⚡ Critical Infrastructure
* 🌐 องค์กรขนาดใหญ่ที่มีระบบ IT ซับซ้อน
* 🎯 องค์กรที่มีความเสี่ยงต่อ **Targeted Attack / APT**

**เน้นมาตรการขั้นสูง เช่น**

* 🔬 การตรวจจับภัยคุกคามเชิงลึก
* 🎯 Threat Detection และ Threat Hunting
* 🕵️ การตรวจสอบพฤติกรรมผู้ใช้งาน
* 📊 การวิเคราะห์ Log และ Security Events ขั้นสูง
* 🚨 Advanced Incident Response
* 🧠 การรับมือกับ Advanced Persistent Threats (APT)
* 🔐 การควบคุมระบบและข้อมูลที่มีความสำคัญสูง

> 🔥 **IG 3 = ระดับความเข้มงวดสูงสุด สำหรับองค์กรที่มีความเสี่ยงสูงหรือเป็นเป้าหมายของการโจมตีขั้นสูง**

---

# 📊 สรุปความแตกต่าง

```text
                 CIS CONTROLS
                      │
          ┌───────────┴───────────┐
          │                       │
        IG 1                    IG 2                    IG 3
   Essential Cyber          Advanced Security       High-Risk Security
       Hygiene                   │                       │
          │                      │                       │
    ┌─────┴─────┐          ┌─────┴─────┐         ┌─────┴─────┐
    │           │          │           │         │           │
 Password    Patching     Logging     Access   Threat      APT
 Asset       Malware      Monitoring  Control  Hunting     Response
 Basic       Security     Detection   Security Advanced   Deep
 Security    Controls     Response    Controls Detection  Defense
```

## 🎯 แนวคิดจำง่าย

```text
IG 1
│
├── 🧹 Basic Cyber Hygiene
├── 🔑 Password
├── 🔄 Patch
├── 💻 Asset
└── 🛡️ Basic Protection
        │
        ▼
IG 2
│
├── 🔎 Monitoring
├── 📝 Logging
├── 🔐 Strong Access Control
├── 🚨 Incident Response
└── 🛡️ Advanced Security
        │
        ▼
IG 3
│
├── 🎯 Threat Hunting
├── 🔬 Advanced Detection
├── 🧠 APT Defense
├── 🚨 Advanced Incident Response
└── 🛡️ High-Risk Security
```

## ⭐ สรุปสั้น ๆ

| Implementation Group | ระดับ       | แนวคิด                        |
| -------------------- | ----------- | ----------------------------- |
| **IG 1**             | 🟢 พื้นฐาน  | Essential Cyber Hygiene       |
| **IG 2**             | 🟡 กลาง/สูง | Advanced Security             |
| **IG 3**             | 🔴 สูงสุด   | High-Risk / Advanced Security |

### 🧠 จำง่าย

> **IG 1 = ป้องกันพื้นฐาน**
> **IG 2 = ตรวจจับและควบคุมให้เข้มขึ้น**
> **IG 3 = รับมือภัยคุกคามขั้นสูง**

**เป้าหมายไม่ได้หมายความว่าทุกองค์กรต้องไปถึง IG 3** แต่ควรเลือก Implementation Group ให้เหมาะสมกับ **ขนาดองค์กร ความเสี่ยง และทรัพยากร** ขององค์กร
