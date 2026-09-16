# Linux Logs & Security Monitoring
## อ่าน Linux Logs ให้เป็น เพื่อค้นหาเหตุการณ์ผิดปกติและภัยคุกคาม

ในการทำ Cybersecurity / Threat Hunting สิ่งหนึ่งที่สำคัญมากคือการรู้ว่า:

> 🕵️‍♂️ "ระบบกำลังบอกอะไรเรา ผ่าน Logs?"

Linux ทุกเครื่องมีการบันทึกเหตุการณ์ต่าง ๆ ที่เกิดขึ้น ไม่ว่าจะเป็นการ Login, การใช้คำสั่ง, Service ทำงานผิดปกติ, Error, การเปลี่ยนแปลงระบบ หรือเหตุการณ์ด้าน Security ดังนั้น Log จึงเปรียบเสมือนกล้องวงจรปิดของระบบ Linux

---

## 1. Log คืออะไร?

Log คือข้อมูลที่ระบบหรือ Application บันทึกไว้เพื่อบอกว่าเกิดเหตุการณ์อะไรขึ้น ตัวอย่างเหตุการณ์สำคัญ:

* 🔑 Login สำเร็จ / ❌ Login ไม่สำเร็จ
* 👤 มีการสร้าง User ใหม่
* 🛠️ Service ถูก Start / Stop
* ⚠️ เกิด Error หรือข้อผิดพลาด
* 🔐 เหตุการณ์ด้าน Security
* 🌐 การเชื่อมต่อ Network
* 📦 การติดตั้ง Package
* 📝 การเปลี่ยนแปลงไฟล์หรือ Configuration

ในการทำ Security Monitoring เราสามารถนำข้อมูลเหล่านี้มาเรียงต่อกันเป็น Timeline ของเหตุการณ์ เช่น:

* `08:21:10` - Failed SSH Login
* `08:21:15` - Failed SSH Login
* `08:21:20` - Failed SSH Login
* `08:21:25` - Successful SSH Login
* `08:21:30` - sudo command executed
* `08:21:40` - New process started

เหตุการณ์แต่ละรายการอาจดูไม่สำคัญเมื่อมองแยกกัน แต่เมื่อนำมาเรียงตามเวลา จะทำให้เห็น Attack Pattern ได้ชัดเจนทันที

---

## 2. Linux เก็บ Logs ไว้ที่ไหน?

บน Linux ส่วนใหญ่ ข้อมูล Log จะถูกจัดเก็บไว้ภายใต้ไดเรกทอรี `/var/log/`

* ตรวจสอบไฟล์ Log ทั้งหมด: `ls -lah /var/log/`

| Distribution Family | Authentication Log | General System Log |
|---|---|---|
| **Debian / Ubuntu** | `/var/log/auth.log` | `/var/log/syslog` |
| **RHEL / CentOS / Rocky** | `/var/log/secure` | `/var/log/messages` |
| **Systemd-based (All)** | `journalctl` | `journalctl` |

> 📌 **ข้อควรระวัง:** ก่อนเขียน Detection Rule หรือ Threat Hunting Script ควรตรวจสอบก่อนว่าระบบปลายทางจัดเก็บ Log รูปแบบใด

---

## 3. Authentication Logs 🔑

Authentication Log มีความสำคัญสูงมากในการตรวจสอบกิจกรรมที่เกี่ยวข้องกับการยืนยันตัวตน (Successful/Failed Login, SSH, sudo, su, PAM)

* **Debian/Ubuntu ตรวจสอบไฟล์ตรง:** `sudo tail /var/log/auth.log`
* **Systemd Journal (ภาพรวม):** `sudo journalctl`
* **เจาะจง SSH Service:** `sudo journalctl -u sshd --no-pager` (หรือ `sudo journalctl -u ssh --no-pager`)

**Attack Pattern ที่ต้องเฝ้าระวัง:**
Failed Login ➔ Failed Login ➔ Failed Login ➔ Successful Login

หากพบ Pattern ลักษณะนี้ ต้องรีบตรวจสอบ Context เพิ่มเติมทันที เช่น Source IP, Target User, ช่วงเวลา และกิจกรรมที่เกิดขึ้นหลังจาก Login สำเร็จ

---

## 4. journalctl — เครื่องมือหลักของ Systemd

สำหรับ Linux ยุคใหม่ที่ใช้ systemd สามารถใช้คำสั่ง `journalctl` เพื่อสืบค้นข้อมูลจาก systemd journal ได้โดยตรง:

* ดู Log ล่าสุด 20 รายการ: `journalctl -n 20`
* ติดตาม Log แบบ Real-time: `journalctl -f` *(กด `Ctrl + C` เพื่อหยุด)*
* ดู Log ของ SSH Service ล่าสุด 100 รายการ: `sudo journalctl -u sshd -n 100 --no-pager`
  * `-u sshd`: กรองเฉพาะ Service `sshd`
  * `-n 100`: จำกัด 100 บรรทัดล่าสุด
  * `--no-pager`: แสดงผลลัพธ์ลงบนหน้าจอ Terminal ทันที ไม่ต้องเปิดผ่าน pager (less)

---

## 5. ค้นหา Log ด้วย grep 🔍

`grep` เป็นเครื่องมือพื้นฐานที่มีประสิทธิภาพสูงในการกรอง Pattern ภายในไฟล์ Log:

* ค้นหาคำว่า failed แบบเจาะจง: `grep "failed" /var/log/auth.log`
* ค้นหาแบบไม่สนใจตัวพิมพ์เล็ก/ใหญ่ (Case-insensitive): `grep -i "error" /var/log/syslog`
  * ตัวเลือก `-i` ช่วยให้ค้นพบคำว่า `error`, `ERROR`, `Error`, และ `ErRoR` ทั้งหมด

---

## 6. เราควร Monitor อะไรใน Linux Logs? 🛡️

* 🔴 **Failed Login ซ้ำ ๆ:** ข้อความเช่น `Failed password`, `authentication failure`, `Invalid user` อาจเป็นสัญญาณของการเดารหัสผ่านหรือ Brute Force Attack
* 🔴 **Unexpected Service Activity:** มีคำสั่ง `systemctl start ...` ใน Service ที่ไม่คุ้นเคย หรือพบ Service แปลกปลอมถูกติดตั้งใหม่
* 🔴 **Unknown Users:** มีการใช้งานคำสั่ง `useradd`, `adduser` หรือปรากฏข้อความ `new user` โดยไม่ได้รับอนุญาต
* 🔴 **Unexpected Errors:** ข้อความบ่งชี้ความผิดปกติ เช่น `error`, `failed`, `denied`, `segfault`, `permission denied` *(ข้อควรจำ: Error ไม่ได้แปลว่าเป็นการโจมตีเสมอไป ต้องตรวจสอบบริบท)*
* 🔴 **Network Connections:** การเชื่อมต่อ Outbound ผิดปกติ, มี Remote IP แปลกปลอม, หรือมี Port เปิด Listening โดยไม่ทราบที่มา
* 🔴 **Changes You Did Not Make:** Configuration, สิทธิ์ไฟล์, หรือ Script ระบบถูกแก้ไขโดยไม่มีใครในทีมรับทราบ

---

## 7. บทบาทของ Linux Logs ต่องาน Cybersecurity 🔐

* **Detect suspicious activity:** ตรวจจับพฤติกรรมผิดปกติได้อย่างรวดเร็ว
* **Investigate incidents:** ใช้เป็นหลักฐานในการสอบสวน Security Incident
* **Troubleshoot problems:** วิเคราะห์หาสาเหตุที่แท้จริงของปัญหา
* **Understand what happened:** เข้าใจลำดับเหตุการณ์ทั้งหมด
* **Build an incident timeline:** ปะติดปะต่อช่วงเวลาของการบุกรุกได้อย่างแม่นยำ

---

## 🧪 Practice Lab (คำสั่งสำหรับฝึกปฏิบัติ)

1. ดู Log ภาพรวมล่าสุด: `journalctl -n 20 --no-pager`
2. ตรวจสอบ Log ของ SSH: `sudo journalctl -u sshd --no-pager` *(หากไม่พบ ให้ลอง `sudo journalctl -u ssh --no-pager`)*
3. ค้นหา Error ในระบบ: `journalctl | grep -i "error"`
4. สำรวจโฟลเดอร์ Log: `ls -lah /var/log/`

---

## 🕵️‍♂️ กรอบความคิด Threat Hunting (5W1H + Next)

อย่าอ่าน Log ด้วยคำถามว่า *"มี Log เยอะขนาดนี้ จะเริ่มดูตรงไหน?"* แต่ให้เริ่มตั้งสมมติฐานด้วยคำถาม:

* **WHO?** — ใครเป็นผู้กระทำ (User, Account ใด)?
* **WHAT?** — เกิดอะไรขึ้น (คำสั่ง, Action ใด)?
* **WHEN?** — เกิดขึ้นเมื่อไหร่ (Timestamp)?
* **WHERE?** — เกิดจากที่ไหน (Source IP, Hostname, Terminal ใด)?
* **HOW?** — กระทำด้วยวิธีใด (SSH, Web Exploit, Cron Job)?
* **WHAT NEXT?** — หลังจากนั้นเกิดอะไรต่อ?

**ตัวอย่างการไล่ล่า:**
Failed SSH Login ➔ Source IP อะไร? ➔ โดน User ไหน? ➔ ความถี่กี่ครั้ง? ➔ Login สำเร็จไหม? ➔ ถ้าสำเร็จ มีการรัน `sudo` หรือไม่? ➔ มี Process แปลก ๆ ถูกสร้างหรือไม่? ➔ มี Connection ส่งออกไปข้างนอกหรือไม่?

---

## 🎯 สรุปภาพรวม (Key Takeaways)

* **Authentication:** Successful Login, Failed Login, sudo / PAM
* **System:** Service, Kernel, System Errors
* **Security:** User Changes, Permission Changes, Suspicious Activity
* **Threat Hunting:** Who, What, When, Where, How, and What happened next?

> 🔥 **Logs Tell a Story:** Log ไม่ได้มีไว้แค่แก้บั๊ก แต่คือหลักฐานชิ้นสำคัญในการทำ Incident Response และจำไว้เสมอว่า *เหตุการณ์ผิดปกติ 1 รายการไม่ได้แปลว่าถูกแฮกทันที ต้องดู Context รอบข้างเสมอ*
