# 🔄 Graylog Log Processing Flow
## ตั้งแต่ Log เข้ามา → Pipeline → Rule → Output → Dashboard

Graylog สามารถนำ Log จากหลายแหล่งเข้ามาประมวลผล ผ่าน Input, Stream, Pipeline และ Rule ก่อนจัดเก็บข้อมูลลง Data Node / OpenSearch แล้วนำไป Search, Aggregate และสร้าง Visualization เพื่อแสดงผลบน Dashboard

---

## 🧠 ภาพรวมการทำงาน

```text
LOG
 │
 ▼
INPUT
 │
 ▼
MESSAGE
 │
 ▼
STREAM
 │
 ▼
PIPELINE
 │
 ├── STAGE
 │    │
 │    ├── RULE
 │    │    │
 │    │    ├── Match
 │    │    │    └── ACTION
 │    │    │         ├── Add Field
 │    │    │         ├── Modify Field
 │    │    │         ├── Parse
 │    │    │         └── Enrich
 │    │    │
 │    │    └── No Match
 │    │
 │    └── STAGE ถัดไป
 │
 ▼
PROCESSED MESSAGE
 │
 ▼
OPENSEARCH / DATA NODE
 │
 ▼
SEARCH
 │
 ▼
AGGREGATION
 │
 ▼
VISUALIZATION
 │
 ▼
DASHBOARD
```

---

# 1. 📝 LOG SOURCE

Log เริ่มต้นมาจากระบบหรืออุปกรณ์ต่าง ๆ เช่น

```text
Linux
Windows
Docker
Wazuh
Firewall
Web Server
Application
Database
Network Device
```

ตัวอย่าง Log จาก Linux:

```text
Failed password for itk from 192.168.1.52
```

---

# 2. 📥 INPUT

Graylog รับ Log ผ่าน Input ที่กำหนดไว้

ตัวอย่าง Input:

```text
Syslog TCP
Syslog UDP
GELF
Beats
Raw/Plaintext
HTTP
```

หน้าที่ของ Input คือ

```text
รับข้อมูล
   │
   ▼
ส่งข้อมูลเข้าสู่ Graylog
```

ตัวอย่าง:

```text
Linux
  │
  │ Syslog TCP
  ▼
Graylog Input
```

---

# 3. 📨 MESSAGE

เมื่อ Graylog รับ Log เข้ามา ระบบจะสร้าง Message

ตัวอย่าง:

```text
source  = lab-linux-node-01
message = Failed password for itk from 192.168.1.52
timestamp = 2026-09-28 05:00:00
```

Message คือหน่วยข้อมูลหลักที่ Graylog ใช้ในการประมวลผล

---

# 4. 🌊 STREAM

หลังจาก Message เข้ามา สามารถกำหนดให้ Message ถูกจัดเข้า Stream ตามเงื่อนไขที่กำหนด

ตัวอย่าง Stream:

```text
SSH Logs
Firewall Logs
Web Logs
Wazuh Alerts
Docker Logs
Authentication Logs
```

ตัวอย่าง:

```text
Message
   │
   ▼
ตรวจสอบเงื่อนไข Stream
   │
   ├── SSH Log
   ├── Firewall Log
   ├── Web Log
   └── Wazuh Alert
```

Stream ช่วยจัดกลุ่ม Message เพื่อให้สามารถนำไปประมวลผลหรือค้นหาได้ง่ายขึ้น

---

# 5. ⚙️ PIPELINE

Message ที่เข้า Stream สามารถถูกส่งเข้าสู่ Pipeline

Pipeline ทำหน้าที่

```text
ตรวจสอบ
   │
   ▼
ประมวลผล
   │
   ▼
เปลี่ยนแปลงข้อมูล
   │
   ▼
เพิ่มข้อมูล
   │
   ▼
ส่งต่อ
```

Pipeline ประกอบด้วย

```text
Pipeline
   │
   ├── Stage 0
   │
   ├── Stage 1
   │
   ├── Stage 2
   │
   └── Stage 3
```

---

# 6. 🧩 STAGE

แต่ละ Pipeline สามารถประกอบด้วยหลาย Stage

ตัวอย่าง:

```text
Pipeline
   │
   ▼
Stage 0
   │
   ▼
Stage 1
   │
   ▼
Stage 2
```

แต่ละ Stage สามารถมี Rule หลายตัว

```text
Stage 0
   │
   ├── Rule 1
   ├── Rule 2
   └── Rule 3
```

หลังจาก Stage ทำงานเสร็จ สามารถกำหนดได้ว่า Message จะดำเนินการต่อไปยัง Stage ถัดไปหรือไม่

---

# 7. 📜 RULE

Rule คือเงื่อนไขที่ใช้ตรวจสอบ Message

ตัวอย่าง:

```text
ถ้า message มีคำว่า "Failed password"
```

สามารถกำหนด Rule ให้ตรวจสอบว่าเป็น SSH Failed Login หรือไม่

ตัวอย่างแนวคิด:

```text
Message
   │
   ▼
มี "Failed password" ?
   │
   ├── YES ──► ทำ Action
   │
   └── NO  ──► ไม่ทำ Action
```

Rule สามารถตรวจสอบ Field ต่าง ๆ เช่น

```text
source
message
source_ip
username
facility
severity
application
```

---

# 8. ⚡ RULE ACTION

เมื่อ Message ตรงตามเงื่อนไขของ Rule แล้ว สามารถทำ Action ได้

ตัวอย่าง Action:

```text
เพิ่ม Field
แก้ไข Field
ลบ Field
Parse ข้อมูล
Extract ข้อมูล
เปลี่ยนค่า Field
เพิ่มข้อมูลสำหรับ Classification
ส่งต่อไปยัง Stream อื่น
```

ตัวอย่าง:

```text
Message
   │
   ▼
Rule:
พบ "Failed password"
   │
   ▼
Action
   │
   ├── event_type = ssh_failed
   ├── username = itk
   ├── source_ip = 192.168.1.52
   └── severity = high
```

---

# 9. 🔍 ตัวอย่างการ Parse Log

สมมติ Log เดิม:

```text
Failed password for itk from 192.168.1.52
```

ก่อนผ่าน Pipeline:

```text
message =
Failed password for itk from 192.168.1.52
```

หลังผ่าน Rule:

```text
event_type = ssh_failed
username   = itk
source_ip  = 192.168.1.52
severity   = high
```

ข้อมูลจึงถูกจัดโครงสร้างให้สามารถนำไปค้นหาและวิเคราะห์ได้ง่ายขึ้น

---

# 10. 🔗 ENRICHMENT

Pipeline ยังสามารถเพิ่มข้อมูลให้กับ Message เพื่อให้วิเคราะห์ได้ละเอียดขึ้น

ตัวอย่าง:

```text
source_ip = 192.168.1.52
```

สามารถนำไปทำ GeoIP Enrichment

```text
source_ip
    │
    ▼
GeoIP
    │
    ├── country
    ├── city
    ├── latitude
    ├── longitude
    └── location
```

หรือเพิ่มข้อมูลอื่น ๆ เช่น

```text
hostname
application
environment
security_category
asset_type
```

---

# 11. 📦 PROCESSED MESSAGE

หลังจากผ่าน Pipeline และ Rules แล้ว Message จะมีข้อมูลที่พร้อมสำหรับการจัดเก็บและวิเคราะห์

ตัวอย่าง:

```text
source          = lab-linux-node-01
username        = itk
source_ip       = 192.168.1.52
event_type      = ssh_failed
severity        = high
country         = Thailand
security_type   = authentication_failure
```

จากเดิมที่เป็น Log ข้อความธรรมดา

```text
Failed password for itk from 192.168.1.52
```

กลายเป็นข้อมูลที่มีโครงสร้างมากขึ้น

```text
event_type = ssh_failed
username   = itk
source_ip  = 192.168.1.52
severity   = high
```

---

# 12. 💾 STORAGE

หลังจาก Message ผ่านการประมวลผลแล้ว จะถูกจัดเก็บในระบบ Storage ของ Graylog

ตัวอย่างสถาปัตยกรรม:

```text
Graylog
   │
   ▼
Data Node
   │
   ▼
OpenSearch
   │
   ▼
Index
```

ข้อมูลที่จัดเก็บแล้วสามารถนำไป

```text
Search
Analyze
Aggregate
Visualize
```

ได้

---

# 13. 🔎 SEARCH

ผู้ดูแลสามารถค้นหา Log ที่ผ่านการประมวลผลแล้ว

ตัวอย่าง:

```text
event_type:ssh_failed
```

หรือ

```text
severity:high
```

หรือ

```text
source_ip:192.168.1.52
```

หรือค้นหาแบบผสม:

```text
event_type:ssh_failed AND severity:high
```

---

# 14. 📊 AGGREGATION

หลังจากค้นหา Message แล้ว สามารถนำข้อมูลจำนวนมากมาวิเคราะห์เป็นสถิติ

ตัวอย่าง:

```text
จำนวน Failed Login
จำนวน Event ตาม Host
จำนวน Event ตาม Source IP
จำนวน Event ตาม Country
จำนวน Event ตาม Severity
จำนวน Event ตามช่วงเวลา
```

ตัวอย่าง:

```text
SSH Failed Login
       │
       ▼
Aggregation
       │
       ├── 192.168.1.52 = 152 Events
       ├── 192.168.1.60 = 87 Events
       └── 192.168.1.70 = 43 Events
```

---

# 15. 📈 VISUALIZATION

ผลจาก Search และ Aggregation สามารถนำมาแสดงเป็น Visualization

ตัวอย่าง:

```text
Line Chart
Bar Chart
Pie Chart
Data Table
Metric
Map
```

ตัวอย่าง:

```text
Failed Login ตามเวลา

Events
  │
  │             ╭──╮
  │        ╭────╯  ╰──╮
  │    ╭───╯           ╰──╮
  │────╯                   ╰────
  └──────────────────────────────► Time
```

---

# 16. 🖥️ DASHBOARD

นำ Visualization หลายตัวมารวมกันเป็น Dashboard

ตัวอย่าง Security Dashboard:

```text
┌──────────────────────────────────────────────────────┐
│             🛡️ SECURITY DASHBOARD                    │
├──────────────────────┬───────────────────────────────┤
│ Failed Login         │ High Severity Events          │
│                      │                               │
│       152            │             23                │
├──────────────────────┴───────────────────────────────┤
│                                                      │
│              Events Over Time                        │
│                                                      │
│       📈 📈 📈 📈 📈 📈 📈                           │
│                                                      │
├──────────────────────┬───────────────────────────────┤
│ Top Source IP        │ Events by Host                │
│                      │                               │
│ 192.168.1.52         │ lab-linux-node-01             │
│ 192.168.1.60         │ lab-linux-node-02             │
│ 192.168.1.70         │ docker-01                     │
├──────────────────────┴───────────────────────────────┤
│                                                      │
│                 🌍 GeoIP Map                         │
│                                                      │
└──────────────────────────────────────────────────────┘
```

---

# 🔄 END-TO-END FLOW

```text
┌───────────────┐
│   LOG SOURCE  │
│ Linux / Docker│
│ Wazuh / FW    │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│     INPUT     │
│ Syslog / GELF │
│ Beats / HTTP  │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│    MESSAGE    │
│ Raw Log       │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│    STREAM     │
│ Classification│
└───────┬───────┘
        │
        ▼
┌────────────────────────┐
│       PIPELINE         │
│                        │
│  ┌──────────────────┐  │
│  │     STAGE 0      │  │
│  │       RULE       │  │
│  └────────┬─────────┘  │
│           │            │
│           ▼            │
│       ACTION           │
│           │            │
│           ▼            │
│  ┌──────────────────┐  │
│  │     STAGE 1      │  │
│  │       RULE       │  │
│  └────────┬─────────┘  │
│           │            │
│           ▼            │
│      ENRICHMENT        │
└───────────┬────────────┘
            │
            ▼
┌────────────────────┐
│ PROCESSED MESSAGE  │
│                    │
│ event_type         │
│ username           │
│ source_ip          │
│ severity            │
└──────────┬─────────┘
           │
           ▼
┌────────────────────┐
│   DATA NODE /      │
│    OPENSEARCH      │
└──────────┬─────────┘
           │
           ▼
┌────────────────────┐
│       SEARCH       │
└──────────┬─────────┘
           │
           ▼
┌────────────────────┐
│    AGGREGATION     │
└──────────┬─────────┘
           │
           ▼
┌────────────────────┐
│  VISUALIZATION     │
│                    │
│ Chart / Metric /   │
│ Table / Map        │
└──────────┬─────────┘
           │
           ▼
┌────────────────────┐
│     DASHBOARD      │
│                    │
│      🛡️ SOC        │
└────────────────────┘
```

---

# 🧠 จำง่ายที่สุด

```text
LOG
 ↓
INPUT
 ↓
MESSAGE
 ↓
STREAM
 ↓
PIPELINE
 ↓
STAGE
 ↓
RULE
 ↓
ACTION
 ↓
ENRICHMENT
 ↓
PROCESSED MESSAGE
 ↓
DATA NODE / OPENSEARCH
 ↓
SEARCH
 ↓
AGGREGATION
 ↓
VISUALIZATION
 ↓
DASHBOARD
```

---

# 🎯 ตัวอย่างจริง: SSH Failed Login

```text
1. Linux Server
       │
       │
       ▼
2. SSH Log
   "Failed password for itk from 192.168.1.52"
       │
       ▼
3. Graylog Input
       │
       ▼
4. Message
       │
       ▼
5. Stream
   "SSH Logs"
       │
       ▼
6. Pipeline
       │
       ▼
7. Rule
   ตรวจสอบ "Failed password"
       │
       ▼
8. Action
   event_type = ssh_failed
   username   = itk
   source_ip  = 192.168.1.52
   severity   = high
       │
       ▼
9. Enrichment
   GeoIP / Host Information
       │
       ▼
10. Processed Message
       │
       ▼
11. OpenSearch
       │
       ▼
12. Search
   event_type:ssh_failed
       │
       ▼
13. Aggregation
   นับจำนวน Failed Login
       │
       ▼
14. Visualization
   Line Chart / Metric / Table
       │
       ▼
15. Dashboard
   🛡️ Security Monitoring
```

---

# 🔥 แนวคิดสำคัญ

```text
INPUT
  │
  │ รับ Log
  ▼
MESSAGE
  │
  │ ข้อมูลดิบ
  ▼
STREAM
  │
  │ จัดกลุ่ม
  ▼
PIPELINE
  │
  │ ประมวลผล
  ▼
RULE
  │
  │ ตรวจสอบ
  ▼
ACTION
  │
  │ เปลี่ยน / เพิ่ม / Parse
  ▼
PROCESSED MESSAGE
  │
  │ จัดเก็บ
  ▼
OPENSEARCH
  │
  │ ค้นหา
  ▼
SEARCH
  │
  │ วิเคราะห์
  ▼
AGGREGATION
  │
  │ แสดงผล
  ▼
VISUALIZATION
  │
  │ รวมหลาย Visualization
  ▼
DASHBOARD
```

> 💡 **Pipeline = สมองในการประมวลผล Log**
>
> 📜 **Rule = เงื่อนไขในการตัดสินใจว่าจะทำอะไรกับ Log**
>
> ⚡ **Action = สิ่งที่ระบบทำเมื่อ Rule ตรงเงื่อนไข**
>
> 🌊 **Stream = การจัดกลุ่ม Message**
>
> 💾 **Data Node / OpenSearch = ที่จัดเก็บข้อมูล**
>
> 🔎 **Search = การค้นหาข้อมูล**
>
> 📊 **Aggregation = การสรุปข้อมูลเป็นสถิติ**
>
> 📈 **Visualization = การแสดงข้อมูลเป็นกราฟ/ตัวเลข/ตาราง**
>
> 🖥️ **Dashboard = หน้าจอรวมข้อมูลเพื่อ Monitoring**
