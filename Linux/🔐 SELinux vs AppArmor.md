🔐 SELinux vs AppArmor: เลือกอะไรเพื่อปกป้อง Linux?

เมื่อพูดถึงการรักษาความปลอดภัยของ Linux หลายคนจะคุ้นเคยกับ SELinux และ AppArmor ซึ่งทั้งสองเป็นระบบ Mandatory Access Control (MAC) ที่ช่วยควบคุมว่า Process สามารถทำอะไรกับระบบได้บ้าง

แนวคิดสำคัญคือ

🛡️ แม้ Process จะได้สิทธิ์ root ก็ไม่ได้หมายความว่าจะสามารถเข้าถึงทุกอย่างในระบบได้

นี่คือหัวใจของ MAC และเป็นชั้นป้องกันที่สำคัญมากสำหรับ Linux Server, Container และระบบที่มีความต้องการด้าน Security สูง

🧠 1. SELinux และ AppArmor คืออะไร?
🟢 SELinux

SELinux — Security-Enhanced Linux

เป็นระบบ MAC ที่ใช้แนวคิด Label-Based Security

กล่าวง่าย ๆ คือ Linux จะกำหนด Security Context / Label ให้กับ Process และ Resource ต่าง ๆ

ตัวอย่างเช่น

Process
httpd_t
   │
   ▼
SELinux Policy
   │
   ▼
Resource
httpd_sys_content_t
   │
   ▼
ALLOW / DENY

ดังนั้น SELinux ไม่ได้มองเพียงว่า

root = ทำได้ทุกอย่าง

แต่จะถามต่อว่า

🔎 Process นี้มี Label อะไร และ Policy อนุญาตให้ Label นี้ทำอะไรได้บ้าง?

🔵 2. AppArmor ทำงานอย่างไร?

AppArmor ใช้แนวคิดที่แตกต่างออกไป คือ Path-Based Security

แทนที่จะเน้น Security Label จะกำหนด Profile ให้กับ Application หรือ Executable

ตัวอย่างเช่น

/usr/sbin/nginx
       │
       ▼
AppArmor Profile
       │
       ▼
Allowed Paths
       │
       ▼
ALLOW / DENY

ตัวอย่าง Profile อาจกำหนดว่า Nginx สามารถอ่าน

/etc/nginx/**
/var/www/**

แต่ไม่สามารถอ่าน

/home/user/private/**

ได้

ดังนั้นแนวคิดหลักคือ

🔵 AppArmor ควบคุม Application ผ่าน Profile และ Path

⚔️ 3. ความแตกต่างที่สำคัญ
🟢 SELinux = Label-Based

SELinux ใช้สิ่งที่เรียกว่า Security Context

ตัวอย่าง:

system_u:system_r:httpd_t:s0

ไฟล์เองก็มี Context เช่น

httpd_sys_content_t

Policy จะตัดสินจากความสัมพันธ์ระหว่าง

Process Context
        +
Resource Context
        +
Policy

จึงสามารถสร้าง Policy ที่ละเอียดมากได้

จุดเด่น

🛡️ Fine-grained control
🔐 เหมาะกับระบบ Security สูง
🏢 เหมาะกับระบบ Enterprise
📦 รองรับแนวคิดการแยก Container ด้วย MCS/SELinux labeling
📋 เหมาะกับ Environment ที่ต้องผ่าน Compliance

🔵 4. AppArmor = Path-Based

AppArmor จะผูก Policy กับ Application Profile

เช่น

/usr/sbin/nginx

แล้วกำหนดว่า Application นี้สามารถเข้าถึง Path ใดได้บ้าง

ตัวอย่างแนวคิด:

/usr/sbin/nginx
        │
        ├── read /etc/nginx/**
        ├── read /var/www/**
        ├── write /var/log/nginx/**
        └── DENY /home/**
จุดเด่น

⚡ เรียนรู้ได้ง่ายกว่า SELinux
📝 Profile อ่านและทำความเข้าใจได้ง่าย
🚀 เหมาะกับการ Deploy อย่างรวดเร็ว
🐧 พบได้บ่อยใน Ubuntu/Debian
🔧 เหมาะกับการสร้าง Policy สำหรับ Application โดยตรง

🏗️ 5. SELinux vs AppArmor ในมุม Security

สิ่งสำคัญคือ ทั้งสองไม่ได้มีหน้าที่แทน Linux Permission

Linux ปกติอาจมี

User
  ↓
UID / GID
  ↓
File Permission
  ↓
rwx

แต่ MAC จะเข้ามาตรวจสอบเพิ่มอีกชั้น

Application
     │
     ▼
Linux DAC
     │
     ▼
MAC
 ┌───┴────┐
SELinux  AppArmor
     │
     ▼
ALLOW / DENY

ดังนั้นแม้ Application จะสามารถผ่าน Linux permission ได้ ก็ยังสามารถถูก MAC ปฏิเสธได้

นี่เป็นเหตุผลที่ MAC มีประโยชน์มากในการป้องกัน Privilege Escalation และ Post-Exploitation

🔥 6. ตัวอย่างสถานการณ์ที่เห็นภาพ

สมมติ Web Server ถูกโจมตี และ Attacker สามารถทำให้ Web Process ทำงานในสิทธิ์สูงได้

หากไม่มี MAC:

Web Server
    │
    ▼
Compromised Process
    │
    ▼
Access sensitive files

แต่เมื่อเปิดใช้ MAC:

Web Server
    │
    ▼
Compromised Process
    │
    ▼
SELinux / AppArmor
    │
    ├── ✅ Allowed
    │
    └── ❌ Denied

ดังนั้น

🔐 การได้ root ไม่ได้แปลว่าจะผ่าน MAC ได้เสมอไป

นี่เป็นแนวคิดสำคัญมากในการออกแบบระบบ Linux Security

🏢 7. แล้วควรเลือก SELinux หรือ AppArmor?
🟢 เลือก SELinux เมื่อ...

เหมาะกับระบบที่ต้องการ Security Control ระดับสูง

เช่น

🛡️ High-Security Environment
🏢 Enterprise Server
🔐 ระบบที่มีข้อมูลสำคัญ
📋 PCI-DSS / CIS / Compliance
📦 Container Isolation
🔎 ต้องการ Policy ที่ละเอียดมาก
🧪 ต้องการควบคุมพฤติกรรม Process อย่างละเอียด

Distribution ที่พบ SELinux เป็นตัวเลือกหลัก เช่น

RHEL
Rocky Linux
AlmaLinux
Fedora

สำหรับผู้ที่ทำ Linux Server และ Security Lab การเรียน SELinux ถือว่ามีประโยชน์มาก โดยเฉพาะเมื่อทำงานกับ RHEL-based systems

🔵 8. เลือก AppArmor เมื่อ...

เหมาะกับผู้ที่ต้องการ

⚡ Deploy ง่าย
📝 Policy เข้าใจง่าย
🔧 Configuration ไม่ซับซ้อนมาก
🐧 ใช้งาน Ubuntu/Debian
🚀 ต้องการป้องกัน Application อย่างรวดเร็ว
👨‍💻 ต้องการเรียนรู้ MAC โดยเริ่มจากแนวคิดที่เข้าใจง่ายกว่า

ตัวอย่าง Distribution ที่พบ AppArmor ได้บ่อย:

Ubuntu
Debian
SUSE
🛠️ 9. คำสั่งสำคัญของ SELinux

ตรวจสอบสถานะ:

sestatus

ตรวจสอบ Mode:

getenforce

ตัวอย่างผลลัพธ์:

Enforcing

หรือ

Permissive

เปลี่ยนเป็น Enforcing ชั่วคราว:

sudo setenforce 1

เปลี่ยนเป็น Permissive:

sudo setenforce 0

ดู Context ของไฟล์:

ls -Z

ดู Context ของ Process:

ps -eZ

ตรวจสอบ Denial:

ausearch -m avc -ts recent

หรือใช้:

sealert

เพื่อช่วยวิเคราะห์ปัญหาที่ SELinux ปฏิเสธ

🔵 10. คำสั่งสำคัญของ AppArmor

ตรวจสอบสถานะ:

sudo aa-status

ดูว่า AppArmor ทำงานอยู่หรือไม่:

sudo aa-enabled

ดู Profile:

sudo aa-status

สร้าง Profile จาก Log:

sudo aa-logprof

Compile Profile:

sudo apparmor_parser -r /etc/apparmor.d/<profile>

Restart Service:

sudo systemctl restart apparmor

แนวคิดสำคัญคือ AppArmor จะมี Profile เช่น

/etc/apparmor.d/usr.sbin.nginx

ซึ่งใช้กำหนดสิทธิ์ของ Nginx

⚠️ 11. Common Mistakes ที่พบบ่อย
❌ 1. เปลี่ยน Mode ทันทีโดยไม่ทดสอบ

เช่นเปลี่ยน SELinux จาก

Permissive

เป็น

Enforcing

โดยไม่ได้ตรวจสอบ Policy

อาจทำให้ Application ทำงานไม่ได้

✅ แนวทางที่ดีกว่า

ทดสอบก่อน → ตรวจสอบ Denial → ปรับ Policy → แล้วค่อย Enforcing

❌ 2. Disable MAC เพื่อแก้ปัญหา

เมื่อ Application ทำงานไม่ได้ บางคนเลือก

Disable SELinux

หรือ

Disable AppArmor

ซึ่งเป็นวิธีแก้ปัญหาที่ง่าย แต่ทำให้ Security Layer หายไป

✅ ควรทำ
ตรวจสอบ Log
      ↓
ค้นหา Policy ที่ Block
      ↓
วิเคราะห์สาเหตุ
      ↓
แก้ Policy

ไม่ใช่

Block
 ↓
Disable Security
🔍 12. อย่าลืมอ่าน Denial Log

นี่เป็นสิ่งสำคัญมากในการทำงานกับ MAC

เมื่อ Application ถูก Block ให้ถามว่า

ใคร?
ทำอะไร?
กับอะไร?
ถูก Block เพราะอะไร?

สำหรับ SELinux ให้ดู AVC:

ausearch -m avc -ts recent

จากนั้นวิเคราะห์ว่า

Source Context
      ↓
Target Context
      ↓
Permission
      ↓
Policy
      ↓
DENIED

ส่วน AppArmor ให้ดู Log ของ Profile และใช้

sudo aa-logprof

เพื่อช่วยสร้างหรือปรับ Profile

📦 13. SELinux กับ Container Security

จุดนี้น่าสนใจมากสำหรับคนที่ทำ Docker / Kubernetes / Container Security

SELinux สามารถช่วยเพิ่ม Isolation ระหว่าง Container ได้ โดยเฉพาะผ่าน Labeling และ MCS

แนวคิดคือ

Container A
   │
   └── SELinux Label A

Container B
   │
   └── SELinux Label B

ทำให้สามารถออกแบบ Policy เพื่อป้องกันไม่ให้ Container หนึ่งเข้าถึง Resource ของอีก Container ได้ง่าย ๆ

นี่เป็นเหตุผลหนึ่งที่ SELinux มีบทบาทสำคัญในระบบ Container และ RHEL-based environments

🧩 14. แล้ว SELinux กับ AppArmor ใช้พร้อมกันได้ไหม?

ประเด็นนี้ต้องระวัง

Linux รุ่นใหม่สามารถรองรับการ LSM stacking ได้ และในบางระบบสามารถมี LSM หลายตัวทำงานร่วมกันได้

แต่ไม่ได้หมายความว่า

❌ เปิด SELinux + AppArmor แล้วจะดีขึ้นเสมอ

เพราะ Policy ซ้อนกันอาจทำให้

Application
    │
    ├── SELinux → ALLOW
    │
    └── AppArmor → DENY
                  ↓
              Application Fail

เกิดปัญหาในการ Troubleshooting ได้

ดังนั้นในทางปฏิบัติ ควรกำหนด MAC หลัก ให้ชัดเจน และเข้าใจว่า LSM ตัวอื่นในระบบกำลังทำงานอยู่หรือไม่

⭐ 15. Best Practices

แนวทางที่ดีในการใช้ MAC คือ

🛡️ 1. Keep MAC Enforcing

อย่าปิด Security Control เพียงเพราะเจอปัญหา Application

🔎 2. Monitor Denials

ตรวจสอบ Log เป็นประจำ

DENIED
   ↓
Investigate
   ↓
Understand
   ↓
Adjust Policy
💾 3. Backup Policy

ก่อนแก้ไข Policy ควรเก็บ Configuration เดิมไว้

Current Policy
      ↓
Backup
      ↓
Change
      ↓
Test
🔐 4. Least Privilege

หลักสำคัญคือ

ให้ Application ทำได้เฉพาะสิ่งที่จำเป็น

ไม่ใช่

ให้ Application ทำทุกอย่าง แล้วค่อย Block สิ่งที่ไม่ต้องการ

📊 5. Monitor MAC Logs

นำ Log จาก SELinux/AppArmor ไปเชื่อมกับระบบ Monitoring หรือ SIEM เช่น Wazuh / Graylog เพื่อสร้าง Detection และ Correlation ได้

ตัวอย่างแนวคิด:

SELinux AVC
     │
     ▼
Log Collection
     │
     ▼
Wazuh / Graylog
     │
     ▼
Detection
     │
     ▼
Alert
     │
     ▼
SOC Investigation
🏁 สรุปแบบจำง่าย
🟢 SELinux

"Who are you, what is your security context, and what does policy allow you to do?"

เน้น

Labels
Contexts
Policy
Fine-grained Control

เหมาะกับ

Enterprise
High Security
Compliance
Container Isolation
🔵 AppArmor

"Which application are you, and which paths are you allowed to access?"

เน้น

Application Profile
Path
Permission
Simplicity

เหมาะกับ

Ubuntu
Debian
Fast Deployment
Application Protection
🎯 สิ่งที่สำคัญที่สุด

อย่ามองว่า

SELinux vs AppArmor = ตัวไหนดีกว่ากัน

แต่ควรมองว่า

🧠 ตัวไหนเหมาะกับ Architecture, Distribution, Application และ Security Requirement ของเรา

เพราะทั้งสองมีเป้าหมายเดียวกันคือ

        Linux Process
             │
             ▼
       ┌─────────────┐
       │     MAC     │
       └──────┬──────┘
              │
        ┌─────┴─────┐
        ▼           ▼
     ALLOW         DENY
        │           │
        ▼           ▼
   ทำงานได้      ถูก Block

🔐 Linux Permission ป้องกันระดับหนึ่ง แต่ MAC ช่วยเพิ่มชั้นป้องกันอีกระดับ

และเมื่อทำ Security จริง ควรคิดเป็น Defense in Depth:

Firewall
   ↓
Linux Permission
   ↓
SELinux / AppArmor
   ↓
Container Isolation
   ↓
eBPF Security
   ↓
Audit / SIEM
   ↓
Detection & Response

นี่คือแนวคิดที่จะทำให้ Linux Server ไม่ได้พึ่งพา Security Control เพียงชั้นเดียว แต่มีหลายชั้นคอยป้องกันเมื่อชั้นใดชั้นหนึ่งถูกหลบเลี่ยงได้ 🔐🛡️
