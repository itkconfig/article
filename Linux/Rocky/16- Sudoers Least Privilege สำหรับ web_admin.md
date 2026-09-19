# 🔐 Sudoers Least Privilege สำหรับ `web_admin` และ Nginx

สคริปต์ชุดนี้เป็นการตั้งค่าในไฟล์ **sudoers** เพื่อจำกัดสิทธิ์แบบ **Least Privilege** ให้กับผู้ใช้ชื่อ `web_admin` สำหรับจัดการเว็บเซิร์ฟเวอร์ **Nginx** โดยอิงแนวคิดด้านความปลอดภัยตามมาตรฐาน **CIS Benchmark**

การตั้งค่านี้แบ่งการทำงานออกเป็น 3 ส่วนหลัก ได้แก่

---

## 1. 🛡️ CIS Hardening Defaults

ส่วนนี้ใช้กำหนดนโยบายความปลอดภัยของ Session และการทำงานของ `sudo`

### `env_reset`

ล้างหรือรีเซ็ต Environment Variables ที่ผู้ใช้ส่งเข้ามาก่อนเรียกใช้คำสั่งผ่าน `sudo`

ช่วยลดความเสี่ยงจากการโจมตีที่อาศัย Environment Variables ที่เป็นอันตราย เช่น การกำหนดตัวแปรที่มีผลต่อการค้นหา Library หรือพฤติกรรมของโปรแกรม

แนวคิดคือ:

```text
User Environment
       │
       ▼
   sudo
       │
       ├── Reset Environment
       │
       ▼
  Trusted Environment
       │
       ▼
 Execute Command
