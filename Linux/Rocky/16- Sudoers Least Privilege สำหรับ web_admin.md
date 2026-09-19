# 🔐 Sudoers Least Privilege สำหรับ `web_admin` และ Nginx

สคริปต์ชุดนี้เป็นการตั้งค่าในไฟล์ **sudoers** เพื่อจำกัดสิทธิ์แบบ **Least Privilege** ให้กับผู้ใช้ชื่อ `web_admin` สำหรับจัดการเว็บเซิร์ฟเวอร์ **Nginx** โดยแบ่งการทำงานออกเป็น 3 ส่วนหลัก ได้แก่

1. 🛡️ CIS Hardening Defaults
2. 🧩 Command Aliases
3. 🔐 Grant Permissions และ Explicit Deny

---

# 1. 🛡️ CIS Hardening Defaults

ส่วนนี้ใช้กำหนดนโยบายด้านความปลอดภัยของ Session และสภาพแวดล้อมการทำงานของ `sudo`

## `env_reset`

ล้างหรือรีเซ็ต Environment Variables ของผู้ใช้ก่อนทำงานผ่าน `sudo`

ช่วยลดความเสี่ยงจากการโจมตีที่อาศัย Environment Variables ที่เป็นอันตราย เช่น การกำหนดค่าที่มีผลต่อการค้นหา Library หรือพฤติกรรมของโปรแกรม

แนวคิด:

    User Environment
           │
           ▼
         sudo
           │
           ▼
     Reset Environment
           │
           ▼
      Execute Command

---

## `secure_path="..."`

กำหนด `PATH` ที่ `sudo` จะใช้ในการค้นหา Executable

ตัวอย่าง:

    /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin

ช่วยป้องกันกรณีผู้ใช้สร้างคำสั่งปลอม เช่น:

    ~/bin/systemctl

แล้วพยายามหลอกให้ `sudo` เรียกไฟล์ปลอมแทน `/usr/bin/systemctl`

แนวคิด:

    User PATH
       │
       ▼
    ไม่ควรเชื่อถือโดยตรง
       │
       ▼
    sudo secure_path
       │
       ▼
    Trusted System Paths

---

## `timestamp_timeout=5`

กำหนดระยะเวลาที่การยืนยันตัวตนของ `sudo` สามารถถูกนำกลับมาใช้ซ้ำได้เป็น 5 นาที

เมื่อเกินช่วงเวลาดังกล่าว ผู้ใช้จะต้องยืนยันตัวตนใหม่เมื่อเรียกใช้ `sudo`

แนวคิด:

    sudo command
         │
         ▼
    Authentication
         │
         ▼
    5 นาที
         │
         ▼
    Authentication ใหม่

ช่วยลดระยะเวลาที่ Credential ของ `sudo` สามารถถูกนำกลับมาใช้ซ้ำได้

---

## `logfile="/var/log/sudo_webadmin.log"`

กำหนดให้ `sudo` บันทึก Log ไปยังไฟล์เฉพาะ เช่น:

    /var/log/sudo_webadmin.log

ช่วยให้สามารถตรวจสอบกิจกรรมของ `web_admin` ได้ง่ายขึ้น เช่น:

- 🔎 Security Audit
- 🕵️ Incident Investigation
- 📋 Compliance
- 📊 SOC Monitoring

> หมายเหตุ: `logfile` เป็นการบันทึกข้อมูลการใช้ sudo ไม่ใช่การบันทึกทุก Keystroke ของผู้ใช้ หากต้องการบันทึก Input/Output ของ Session ควรพิจารณา `log_input` และ `log_output` เพิ่มเติม

---

## `#Defaults:web_admin noexec`

บรรทัดนี้ถูก Comment ไว้ ดังนั้นปัจจุบันยังไม่ได้เปิดใช้งาน

หากเปิดใช้งาน:

    Defaults:web_admin noexec

`sudo` จะพยายามป้องกันโปรแกรมที่ได้รับอนุญาตไม่ให้เรียก Executable อื่นหรือ Shell ต่อออกมา

แนวคิด:

    sudo allowed-command
            │
            ├── Execute
            │
            └── ❌ Shell / Child Process

อย่างไรก็ตาม `noexec` ไม่ใช่กลไกที่รับประกันการป้องกัน Shell Escape ได้กับทุกโปรแกรมหรือทุกวิธีการ จึงควรใช้ร่วมกับการกำหนด Command ที่อนุญาตอย่างเฉพาะเจาะจง

---

# 2. 🧩 Command Aliases

ส่วนนี้ใช้จัดกลุ่มคำสั่งที่ `web_admin` สามารถใช้งานได้

หลักสำคัญของ Least Privilege คือ:

    ไม่ใช่
    "ให้สิทธิ์ root แล้วค่อยห้ามบางอย่าง"

    แต่ควรเป็น
    "ให้เฉพาะสิ่งที่จำเป็นต่อการทำงาน"

---

# 🔧 NGINX_SVC

กำหนดคำสั่งที่ใช้จัดการ Service ของ Nginx

อนุญาตเฉพาะคำสั่งที่จำเป็น เช่น:

- `restart`
- `reload`
- `status`
- `nginx -t`

ตัวอย่าง:

    sudo systemctl status nginx
    sudo systemctl reload nginx
    sudo systemctl restart nginx
    sudo nginx -t

### ❌ ไม่อนุญาต `stop`

แนวคิด:

    restart  → ✅
    reload   → ✅
    status   → ✅
    nginx -t → ✅
    stop     → ❌

การไม่อนุญาต `stop` ช่วยลดโอกาสที่ผู้ใช้จะสั่งหยุด Nginx โดยตรง

> หมายเหตุ: `restart` สามารถทำให้บริการหยุดชั่วคราวระหว่างการ Restart ได้ ดังนั้นการไม่อนุญาต `stop` ไม่ได้หมายความว่า Nginx จะไม่มีทางหยุดทำงานจากสิทธิ์ชุดนี้

---

# 📜 NGINX_LOGS

กำหนดสิทธิ์สำหรับการตรวจสอบ Log ของ Nginx

ตัวอย่าง:

    journalctl -u nginx

ใช้สำหรับตรวจสอบ Log ของ Service

และ:

    tail /var/log/nginx/...

ใช้สำหรับดู Log Files ของ Nginx

แนวคิด:

    web_admin
        │
        ├── ดู Service Log
        │
        └── ดู Nginx Log
                 │
                 ▼
              Read Only

ควรระบุ Path ของไฟล์ที่อนุญาตให้ชัดเจนที่สุด และหลีกเลี่ยง Wildcard ที่กว้างเกินความจำเป็น

---

# 📝 NGINX_CONF

กำหนดให้การแก้ไข Configuration ใช้ `sudoedit`

แทนการให้สิทธิ์ Text Editor โดยตรง เช่น:

    sudo nano /etc/nginx/nginx.conf

หรือ:

    sudo vi /etc/nginx/nginx.conf

ให้ใช้:

    sudoedit /etc/nginx/nginx.conf

## ทำไม `sudoedit` จึงเหมาะกว่า?

หลักการของ `sudoedit` คือให้ผู้ใช้แก้ไขไฟล์ผ่าน Editor ใน Context ของผู้ใช้ทั่วไป แล้วให้ `sudo` จัดการการเขียนไฟล์กลับไปยังตำแหน่งที่มีสิทธิ์สูง

แนวคิด:

    /etc/nginx/nginx.conf
              │
              ▼
           sudoedit
              │
              ▼
        Temporary Copy
              │
              ▼
          User Editor
              │
              ▼
          Save Changes
              │
              ▼
       sudo เขียนกลับ
              │
              ▼
    /etc/nginx/nginx.conf

ช่วยลดความเสี่ยงจากการให้สิทธิ์ `root` กับ Text Editor โดยตรง

Editor บางชนิดสามารถมีความสามารถในการเรียก Command หรือ Shell ได้ เช่น:

    Editor
       │
       ├── Open File
       ├── Execute Command
       ├── Spawn Process
       └── Shell Escape

ดังนั้น:

    sudo vi

หรือ:

    sudo nano

อาจมีความเสี่ยงมากกว่าการออกแบบสิทธิ์ด้วย `sudoedit` อย่างเหมาะสม

> `sudoedit` ไม่ได้ทำให้การแก้ไข Configuration ปลอดภัยโดยอัตโนมัติทุกกรณี ต้องกำหนด Path และสิทธิ์ของไฟล์อย่างรัดกุม และตรวจสอบว่าไฟล์ที่แก้ไขไม่สามารถนำไปใช้เพื่อยกระดับสิทธิ์ได้

---

# 3. 🔐 Grant Permissions

ส่วนนี้เป็นการนำ Command Alias ที่สร้างไว้มาเชื่อมกับผู้ใช้ `web_admin`

แนวคิด:

    web_admin
        │
        ▼
       sudo
        │
        ├── NGINX_SVC
        ├── NGINX_LOGS
        └── NGINX_CONF

---

## `web_admin ALL=(root)`

หมายความว่า `web_admin` สามารถใช้คำสั่งที่ได้รับอนุญาต โดยให้คำสั่งนั้นทำงานในสิทธิ์ของ `root`

แต่ไม่ได้หมายความว่า:

    web_admin = root

ทั้งหมด

เพราะสิทธิ์ถูกจำกัดด้วย Command Alias ที่กำหนดไว้

แนวคิด:

    web_admin
        │
        ▼
       sudo
        │
        ├── Nginx Service → ✅
        ├── Nginx Logs    → ✅
        ├── Nginx Config  → ✅
        │
        ├── bash          → ❌
        ├── sh            → ❌
        └── su            → ❌

นี่คือแนวคิดสำคัญของ **Least Privilege**

---

# 🚫 Explicit Deny Rules

ตัวอย่าง:

    !/usr/bin/su
    !/usr/bin/sh
    !/usr/bin/bash

มีจุดประสงค์เพื่อระบุคำสั่งที่ไม่ต้องการให้ `web_admin` เรียกผ่านกฎ sudoers

ตัวอย่างที่ควรถูกปฏิเสธ:

    sudo /usr/bin/bash
    sudo /usr/bin/sh
    sudo /usr/bin/su

---

# ⚠️ จุดสำคัญเกี่ยวกับ Deny Rules

ไม่ควรพึ่งพาเพียง:

    !/usr/bin/bash
    !/usr/bin/sh
    !/usr/bin/su

เพื่อพิสูจน์ว่า `web_admin` จะไม่สามารถได้ Root Shell

เพราะ Linux มีโปรแกรมจำนวนมากที่สามารถสร้าง Child Process หรือเรียก Shell ได้ เช่น:

- `vim`
- `less`
- `man`
- `awk`
- `perl`
- `python`
- `tar`
- `find`
- `git`

ดังนั้นการออกแบบที่ปลอดภัยกว่าคือ:

    ❌ อย่าให้สิทธิ์ root แบบกว้าง
              │
              ▼
    ✅ Allow เฉพาะ Command ที่จำเป็น
              │
              ▼
    ✅ จำกัด Argument ให้แคบที่สุด
              │
              ▼
    ✅ ใช้ sudoedit สำหรับ Configuration
              │
              ▼
    ✅ ตรวจสอบสิทธิ์ของไฟล์
              │
              ▼
    ✅ Audit การใช้งาน sudo

---

# 🧠 Security Architecture

ภาพรวมของแนวคิดทั้งหมด:

                    web_admin
                        │
                        ▼
                      sudo
                        │
             ┌──────────┴──────────┐
             │                     │
          Defaults              Commands
             │                     │
       ┌─────┼─────┐        ┌─────┼─────┐
       │     │     │        │     │     │
  env_reset  │  logfile   SVC   LOGS  CONF
              │
       secure_path
              │
    timestamp_timeout=5
                              │
                              ▼
                         Nginx Admin
                              │
          ┌───────────────────┼──────────────────┐
          │                   │                  │
          ▼                   ▼                  ▼
       Service              Logs             Config
          │                   │                  │
       reload              journalctl         sudoedit
       restart             tail               │
       status                                  ▼
       nginx -t                         Nginx Configuration

---

# 🎯 หลักการ Least Privilege

เป้าหมายของ Configuration นี้ไม่ใช่การทำให้ `web_admin` เป็น Root เต็มรูปแบบ แต่เป็นการให้สิทธิ์เฉพาะงานที่จำเป็นต่อการดูแล Nginx

แนวคิด:

                         ROOT
                          │
               ┌──────────┴──────────┐
               │                     │
            Full Access          web_admin
                                     │
                                     ▼
                            Limited sudo access
                                     │
                     ┌───────────────┼───────────────┐
                     │               │               │
                     ▼               ▼               ▼
                  Service           Logs          Config
                     │               │               │
                  Limited          Read          sudoedit
                  Commands          Only           Only

หลักการสำคัญ:

> 🔐 **Grant only what is required — deny everything else by default.**

หรือในมุมมองของ SOC / System Administration:

    Least Privilege
          +
    Explicit Allow
          +
    Restricted Arguments
          +
    sudo Logging
          +
    Configuration Control
          +
    Audit
          =
    Reduced Privilege Escalation Risk

---

# 🔍 ตรวจสอบ Configuration ก่อนใช้งาน

หลังแก้ไข `/etc/sudoers` หรือสร้างไฟล์ใน `/etc/sudoers.d/` ควรตรวจสอบ Syntax ก่อน:

    sudo visudo -c

ตรวจสอบสิทธิ์ที่ `web_admin` ได้รับ:

    sudo -l -U web_admin

ควรตรวจสอบ:

    ✅ Allowed Commands
    ❌ Forbidden Commands
    ✅ Command Arguments
    ✅ File Permissions
    ✅ sudo Logs

---

# 🛡️ สรุป

Configuration ชุดนี้ใช้แนวคิดสำคัญของ Linux Security ได้แก่:

1. 🔐 Least Privilege
2. 🔐 Explicit Command Allowlist
3. 🔐 Restricted sudo Environment
4. 🔐 `secure_path`
5. 🔐 sudo Authentication Timeout
6. 🔐 sudo Audit Logging
7. 🔐 `sudoedit` สำหรับ Configuration
8. 🔐 จำกัด Service Management
9. 🔐 จำกัด Log Access
10. 🔐 ลดโอกาสในการเปลี่ยนจาก Nginx Administrator → Root Shell

เป้าหมายสุดท้ายคือ:

    web_admin
       │
       └── สามารถดูแล Nginx ได้
                │
                ├── ตรวจสอบสถานะ
                ├── Reload / Restart
                ├── ตรวจสอบ Configuration
                ├── อ่าน Log
                └── แก้ไข Configuration
                        │
                        ▼
                 ไม่จำเป็นต้องมี
                 Full Root Shell

นี่คือแนวคิดของ:

**Least Privilege + Defense in Depth**

ซึ่งช่วยลดสิทธิ์ที่ไม่จำเป็น และลดพื้นที่ความเสี่ยงจากการนำสิทธิ์ `sudo` ไปใช้ในทางที่ไม่ควร
