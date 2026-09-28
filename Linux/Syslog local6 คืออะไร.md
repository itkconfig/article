# 🔐 Syslog `local6` คืออะไร?

`local6` คือ **Syslog Facility หมายเลข 22** ที่ระบบกำหนดไว้สำหรับให้ผู้ดูแลระบบหรือ Application ใช้เป็น **ช่อง Log แบบกำหนดเอง (Custom Facility)**

พูดง่าย ๆ คือ `local6` **ไม่ใช่ชื่อ Service โดยเฉพาะ** และไม่ได้หมายความว่าเป็น Log ของ "Local เครื่องที่ 6"

แต่ `local6` เป็นหนึ่งใน Facility ที่ Syslog เตรียมไว้ให้เราเลือกใช้งาน

---

# 🧩 โครงสร้างของ Syslog Facility

Syslog แบ่ง Log ออกเป็นกลุ่ม ๆ เรียกว่า **Facility**

    Syslog Facility
    │
    ├── auth / authpriv   → Authentication
    ├── cron              → Cron / Scheduled jobs
    ├── daemon            → System daemons
    ├── kern              → Kernel
    ├── mail              → Mail system
    ├── user              → User-level messages
    ├── local0            → Custom
    ├── local1            → Custom
    ├── local2            → Custom
    ├── local3            → Custom
    ├── local4            → Custom
    ├── local5            → Custom
    ├── local6            → Custom  ← ตัวนี้
    └── local7            → Custom

`local0` ถึง `local7` จึงมีไว้สำหรับ **Application หรือ Administrator ที่ต้องการแยกประเภท Log เอง**

---

# 🎯 แล้ว `local6` ใช้ทำอะไร?

ในระบบ Linux Security / SOC สามารถนำ `local6` มาใช้เป็นช่องสำหรับส่ง **Custom หรือ Security Log**

ตัวอย่าง Architecture:

    auditd
       │
       ▼
    audisp-syslog
       │
       │ facility = local6
       ▼
    rsyslog
       │
       │ local6.*
       ▼
    Graylog
    192.168.1.11:1514

สมมติ Audit Log ถูกส่งออกมาแบบนี้:

    local6.info: type=USER_LOGIN msg=audit(...)

rsyslog จะเห็นว่า Facility คือ:

    local6

และ Severity คือ:

    info

ดังนั้นกฎนี้:

    local6.*

จะจับ Log นี้ได้

---

# 🔎 `local6.*` อ่านอย่างไร?

`local6.*` แยกออกเป็น 2 ส่วน:

    local6.*
    │      │
    │      └── Severity
    │
    └───────── Facility

## `local6`

หมายถึง:

> Log นี้มาจาก Facility `local6`

## `*`

หมายถึง:

> ทุก Severity

ดังนั้น:

    local6.*

จะครอบคลุม Severity เช่น:

    local6.emerg
    local6.alert
    local6.crit
    local6.err
    local6.warning
    local6.notice
    local6.info
    local6.debug

---

# 🛡️ ทำไมจึงสามารถใช้ `local6` กับ Audit Log?

`auditd` สามารถสร้างข้อมูลด้าน Security จำนวนมาก เช่น:

    USER_LOGIN
    USER_START
    USER_END
    USER_ACCT
    USER_CMD
    EXECVE
    SYSCALL
    CRED_ACQ
    CRED_DISP
    AVC

ถ้าเอา Log เหล่านี้ไปรวมกับ Facility ทั่วไป อาจทำให้การแยกประเภท Log ทำได้ยาก

เราจึงสามารถออกแบบเส้นทางให้ Security/Audit Log ใช้ Facility ที่กำหนดเอง เช่น `local6`

    Audit
       ↓
    local6
       ↓
    rsyslog
       ↓
    Graylog

ข้อดีคือฝั่ง Graylog สามารถนำ Facility มาใช้ในการ Filter หรือสร้าง Stream ได้

ตัวอย่าง Query:

    syslog_facility:local6

สามารถนำไปสร้าง Stream สำหรับ:

    Linux Audit Logs

โดยเฉพาะได้

---

# 🧪 ตัวอย่างในระบบ Linux Security

สมมติว่า `auditd` ตรวจพบการใช้ `sudo`

Audit Event อาจมีข้อมูลลักษณะนี้:

    type=USER_CMD
    auid=1000
    uid=0
    exe="/usr/bin/sudo"

ถ้า `audisp-syslog` ส่ง Event ออกมาด้วย Facility `local6` จะมีลักษณะประมาณ:

    local6.info: type=USER_CMD ...

จากนั้น rsyslog มีกฎ:

    local6.* action(
        type="omfwd"
        target="192.168.1.11"
        port="1514"
        protocol="tcp"
    )

ผลลัพธ์คือ Log จะถูก Forward ไป Graylog

---

# 🔄 Flow ตั้งแต่ Audit Log จนถึง Graylog

                        Linux
                          │
                          ▼
                       auditd
                          │
                          ▼
                   audisp-syslog
                          │
                          │ local6.info
                          ▼
                       rsyslog
                          │
                       local6.*
                          │
                          ▼
                       omfwd
                          │
                      TCP/1514
                          │
                          ▼
                  Graylog 192.168.1.11

---

# ⚠️ `local6` ไม่ได้เป็นตัว "ส่ง Log"

จุดนี้สำคัญมาก

`local6` เป็นเพียง **Facility หรือป้ายกำกับประเภทของ Log**

มันไม่ได้ทำหน้าที่ส่ง Log เอง

สามารถมองภาพได้แบบนี้:

    Log Message
        │
        ├── Facility = local6
        │
        ├── Severity = info
        │
        └── Message = "USER_CMD ..."

จากนั้น `rsyslog` เป็นตัวประมวลผลและตัดสินใจว่าจะนำ Log ไปที่ไหน

    local6.*
        │
        ▼
    rsyslog Rule
        │
        ▼
    omfwd
        │
        ▼
    Graylog

---

# 🧠 Facility กับ Severity ต่างกันอย่างไร?

Syslog Message สามารถมองง่าย ๆ เป็น:

    Facility + Severity + Message

ตัวอย่าง:

    local6.info: type=USER_CMD ...

แยกได้เป็น:

    local6
      │
      └── Facility
          "Log มาจากกลุ่มไหน?"

    info
      │
      └── Severity
          "Log มีระดับความสำคัญเท่าไร?"

    type=USER_CMD
      │
      └── Message
          "เกิดเหตุการณ์อะไร?"

ดังนั้น:

    Facility = กลุ่มของ Log
    Severity = ระดับของ Log
    Message  = รายละเอียดของเหตุการณ์

---

# 🔍 `local6.*` กับ `local6.info` ต่างกันอย่างไร?

## `local6.*`

    local6.*

หมายถึง:

> รับทุก Severity ของ Facility `local6`

เช่น:

    local6.emerg
    local6.alert
    local6.crit
    local6.err
    local6.warning
    local6.notice
    local6.info
    local6.debug

---

## `local6.info`

    local6.info

ใน Syntax ของ rsyslog แบบ selector จะหมายถึง `local6` ที่มี Severity `info` และ Severity ที่มีค่าความสำคัญสูงกว่า `info`

ดังนั้นถ้าต้องการเลือกเฉพาะ `info` ต้องใช้:

    local6.=info

ความหมายคือ:

> เอาเฉพาะ Severity `info` ของ Facility `local6`

---

# 🏗️ ตัวอย่าง Architecture สำหรับ SOC Lab

สามารถออกแบบ Pipeline ได้ดังนี้:

    ┌─────────────────────────────────────────┐
    │              Linux Server               │
    │                                         │
    │  auditd                                 │
    │    │                                    │
    │    ▼                                    │
    │  audisp-syslog                          │
    │    │                                    │
    │    │ Facility = local6                  │
    │    ▼                                    │
    │  rsyslog                                │
    └──────────────┬──────────────────────────┘
                   │
                   │ local6.*
                   ▼
            ┌──────────────┐
            │    omfwd     │
            │ TCP :1514    │
            └──────┬───────┘
                   │
                   ▼
            ┌──────────────┐
            │    Graylog   │
            │ 192.168.1.11 │
            └──────┬───────┘
                   │
                   ▼
            ┌──────────────┐
            │   Streams    │
            └──────┬───────┘
                   │
                   ▼
            ┌──────────────┐
            │  Pipelines   │
            └──────┬───────┘
                   │
                   ▼
            ┌──────────────┐
            │   Alerts /   │
            │  Dashboard   │
            └──────────────┘

---

# 🔐 ตัวอย่างการใช้งานกับ Graylog

ตัวอย่าง rsyslog Rule:

    authpriv.*;auth.*;local6.*;cron.*;kern.* action(
        type="omfwd"
        target="192.168.1.11"
        port="1514"
        protocol="tcp"
        template="RSYSLOG_SyslogProtocol23Format"
        queue.type="linkedList"
        queue.filename="graylog_forward"
        queue.size="100000"
        queue.maxdiskspace="1g"
        queue.saveonshutdown="on"
        queue.workerThreads="2"
        queue.dequeueBatchSize="1000"
        action.resumeRetryCount="-1"
        action.resumeInterval="10"
    )

ใน Rule นี้:

    authpriv.*  → Authentication / Authorization Logs
    auth.*      → Authentication / Authorization Logs
    local6.*    → Custom / Security Logs
    cron.*      → Cron / Scheduled Jobs
    kern.*      → Kernel Logs

เครื่องหมาย `;` หมายถึง **OR**

ดังนั้น:

    authpriv.*;auth.*;local6.*;cron.*;kern.*

มีความหมายประมาณว่า:

    authpriv.*
    OR
    auth.*
    OR
    local6.*
    OR
    cron.*
    OR
    kern.*

ถ้า Log ตรงกับ Facility ใด Facility หนึ่ง ก็จะเข้าสู่ `action(...)`

---

# 🎯 จำง่าย ๆ

    Facility
       ↓
    "Log มาจากกลุ่มไหน?"

    Severity
       ↓
    "Log มีระดับความสำคัญเท่าไร?"

    local6
       ↓
    "Custom Facility ที่เราเลือกใช้"

    local6.*
       ↓
    "ทุก Severity ของ Facility local6"

    rsyslog
       ↓
    "ตัวประมวลผลและส่ง Log"

    omfwd
       ↓
    "ตัว Forward Log"

    Graylog
       ↓
    "ตัวรับ วิเคราะห์ ค้นหา Alert และสร้าง Dashboard"

---

# 🔐 สรุปสำหรับ SOC

`local6` **ไม่ใช่ Service และไม่ใช่โปรแกรม**

แต่เป็น **Syslog Facility สำหรับ Custom Logging**

ในระบบ SOC เราสามารถกำหนดให้ Security/Audit Log ใช้ `local6` เพื่อให้สามารถแยกเส้นทางของ Log ได้ง่าย

    Security Event
          ↓
        auditd
          ↓
    audisp-syslog
          ↓
        local6
          ↓
       rsyslog
          ↓
        omfwd
          ↓
    Graylog TCP/1514
          ↓
       Stream
          ↓
      Pipeline
          ↓
       Alert
          ↓
      Dashboard

> 💡 **จำประโยคเดียว:**
>
> **`local6` คือ "ป้าย Facility แบบ Custom" ที่เราใช้จัดกลุ่ม Log ไม่ใช่ตัวส่ง Log ส่วน `rsyslog` ต่างหากที่ทำหน้าที่ประมวลผลและ Forward Log ไปยัง Graylog**
