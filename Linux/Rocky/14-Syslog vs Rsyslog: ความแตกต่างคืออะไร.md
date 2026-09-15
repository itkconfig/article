# 🔐 Syslog vs Rsyslog: ความแตกต่างคืออะไร?

## 1. Syslog คืออะไร?

**Syslog** ไม่ได้หมายถึงโปรแกรมเพียงตัวเดียว แต่โดยทั่วไปหมายถึงมาตรฐานและกลไกสำหรับการส่งและจัดการข้อความ Log ของระบบ

แนวคิดของ Syslog คือ:

Application / Service  
↓  
Syslog Message  
↓  
Syslog Daemon  
↓  
├── Local Log File  
├── Remote Syslog Server  
└── Other Destination

ตัวอย่าง Log:

`Sep 16 06:20:01 lab-linux-node-01 sshd[1234]: Accepted publickey for itk`

Syslog กำหนดแนวทางเกี่ยวกับสิ่งต่าง ๆ เช่น:

- Facility
- Severity / Priority
- Message Format
- การส่ง Log
- การส่ง Log ไปยัง Remote Server

### Facility

ใช้บอกว่า Log มาจากส่วนใดของระบบ เช่น:

`auth`  
`authpriv`  
`cron`  
`daemon`  
`kern`  
`mail`  
`user`  
`local0 - local7`

### Severity

ใช้บอกระดับความสำคัญของ Log:

`emerg`  
`alert`  
`crit`  
`err`  
`warning`  
`notice`  
`info`  
`debug`

ดังนั้น Syslog จึงเปรียบเสมือน **ภาษากลางสำหรับ Log**

---

# 2. Rsyslog คืออะไร?

**Rsyslog (Rocket-fast System for Log processing)** คือ Software ที่ทำหน้าที่เป็น Syslog daemon

พูดง่าย ๆ คือ:

**Syslog = มาตรฐาน / กลไก**

**Rsyslog = โปรแกรมที่นำมาตรฐานนั้นมาใช้งาน**

บน Linux สามารถตรวจสอบ Rsyslog ได้ด้วย:

`systemctl status rsyslog`

ไฟล์ Configuration หลัก:

`/etc/rsyslog.conf`

และ Configuration เพิ่มเติม:

`/etc/rsyslog.d/`

---

# 3. Rsyslog ทำอะไรได้มากกว่า Syslog แบบดั้งเดิม?

Rsyslog มีความสามารถในการจัดการ Log ที่หลากหลาย เช่น:

Application  
↓  
Rsyslog  
↓  
├── `/var/log/messages`  
├── `/var/log/secure`  
├── `/var/log/audit/...`  
├── Remote Syslog Server  
├── Graylog  
├── SIEM  
└── Custom Destination

สามารถกำหนดเงื่อนไขได้ เช่น:

ถ้า `facility = authpriv`

และ `severity >= info`

ให้เขียนไปที่:

`/var/log/secure`

หรือสามารถส่ง Log ไปยัง Remote Server:

Linux Server  
↓  
TCP/UDP Syslog  
↓  
Graylog / Syslog Server

---

# 4. ตัวอย่าง Configuration ของ Rsyslog

ตัวอย่าง:

`authpriv.*    /var/log/secure`

หมายความว่า:

- Facility = `authpriv`
- Severity = ทุกระดับ
- Destination = `/var/log/secure`

อีกตัวอย่าง:

`*.info;mail.none;authpriv.none;cron.none    /var/log/messages`

หมายถึงให้ส่ง Log ระดับ `info` ขึ้นไปไปที่:

`/var/log/messages`

แต่ยกเว้น:

- `mail`
- `authpriv`
- `cron`

---

# 5. Rsyslog สามารถรับ Log จากเครื่องอื่นได้

ตัวอย่าง Architecture:

Linux Server 1  
↓  
Rsyslog  
↓  
TCP/UDP 514  
↓  
Rsyslog Server  
↓  
`/var/log/remote/...`  
↓  
Graylog

ดังนั้น Rsyslog สามารถทำหน้าที่เป็นทั้ง:

- **Syslog Client**
- **Syslog Server**

ได้

---

# 6. Syslog กับ Rsyslog ต่างกันอย่างไร?

| หัวข้อ | Syslog | Rsyslog |
|---|---|---|
| ประเภท | มาตรฐาน / กลไก | Software |
| หน้าที่ | กำหนดแนวทาง Syslog | รับ / ประมวลผล / ส่ง Log |
| เป็น Service หรือไม่ | ไม่ใช่โปรแกรมเฉพาะตัว | เป็น Service |
| Configuration | ขึ้นกับ Syslog implementation | `/etc/rsyslog.conf` |
| Remote Logging | เป็นแนวคิด / มาตรฐาน | รองรับ |
| Filtering | ขึ้นกับ implementation | รองรับขั้นสูง |
| Queue | ขึ้นกับ implementation | รองรับ |
| TCP | รองรับตาม implementation | รองรับ |
| TLS | ขึ้นกับ implementation | รองรับ |
| Forwarding | ขึ้นกับ implementation | รองรับ |
| ใช้กับ Graylog | ได้ผ่าน Syslog | ได้โดยตรงผ่าน Rsyslog |

---

# 7. Syslog ไม่ได้เท่ากับ Rsyslog

จุดนี้สำคัญมาก

อย่าเข้าใจว่า:

`Syslog = Rsyslog`

แต่ให้เข้าใจว่า:

Syslog  
(มาตรฐาน / แนวคิด)  
↓  
Syslog Daemon  
↓  
├── rsyslog  
├── syslog-ng  
└── implementation อื่น ๆ

ใน Linux อาจพบ Software ที่เกี่ยวข้องกับระบบ Logging หลายตัว เช่น:

- `rsyslog`
- `syslog-ng`
- `systemd-journald`

โดย Rsyslog เป็นหนึ่งในโปรแกรมที่สามารถทำหน้าที่เป็น **Syslog daemon**

---

# 8. แล้ว Rocky Linux ใช้อะไร?

ใน Rocky Linux สมัยใหม่จะมี:

`systemd-journald`

ทำหน้าที่เก็บ Journal ของระบบ

และสามารถมี:

`rsyslog`

ทำหน้าที่รับ / ประมวลผล / ส่ง Syslog ต่อได้

Architecture จึงอาจเป็น:

Linux  
↓  
├── systemd-journald  
│       ↓  
│     Journal  
│  
└── Rsyslog  
        ├── `/var/log/messages`  
        ├── `/var/log/secure`  
        └── Graylog

ดังนั้นการมี `journald` ไม่ได้หมายความว่า Rsyslog ไม่มีประโยชน์

ทั้งสองสามารถทำงานร่วมกันได้

---

# 9. ตัวอย่างในระบบ Security Lab

สมมติว่าเกิด SSH Login:

SSH  
↓  
sshd  
↓  
journald / syslog  
↓  
rsyslog  
↓  
├── `/var/log/secure`  
│  
└── Graylog

หากใช้ Wazuh ด้วย อาจมีอีกเส้นทางหนึ่ง:

SSH  
↓  
`/var/log/secure`  
↓  
├── Wazuh Agent  
│       ↓  
│   Wazuh Manager  
│  
└── Rsyslog  
        ↓  
      Graylog

จึงสามารถใช้ Log เพื่อทำ:

Logging  
↓  
├── Graylog  
├── Wazuh  
└── Security Monitoring

---

# 🎯 สรุปสั้น ๆ

จำง่าย ๆ:

**Syslog**  
= มาตรฐาน / รูปแบบ / กลไกของระบบ Logging

**Rsyslog**  
= โปรแกรมที่ทำหน้าที่เป็น Syslog daemon

หรือจำเป็นประโยคเดียว:

> **Syslog คือ "กติกา" ส่วน Rsyslog คือ "โปรแกรมที่นำกติกานั้นมาใช้งานจริง"**

และใน Linux Security Lab:

Application  
↓  
journald / Syslog  
↓  
Rsyslog  
↓  
├── Local Log  
├── Graylog  
└── Remote Syslog Server

ดังนั้นเวลาพูดว่า **"ส่ง Log ผ่าน Syslog ไป Graylog"** ไม่ได้หมายความว่าต้องมีโปรแกรมชื่อ Syslog โดยเฉพาะ แต่หมายถึงการใช้ **Syslog protocol / format** และอาจใช้ **Rsyslog เป็นตัวส่ง Log** ไปยัง Graylog
