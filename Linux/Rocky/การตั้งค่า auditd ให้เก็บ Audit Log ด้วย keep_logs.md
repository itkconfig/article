# การตั้งค่า `auditd` ให้เก็บ Audit Log ด้วย `keep_logs`

คำสั่งนี้ใช้สำหรับ **แก้ไขการตั้งค่า `auditd`** เพื่อกำหนดว่าเมื่อไฟล์ Audit Log มีขนาดถึงขีดจำกัดแล้ว ให้ **เก็บไฟล์ Log เดิมไว้ (`keep_logs`)** แทนการลบทิ้งหรือทำพฤติกรรมอื่นตามค่าที่กำหนด

```bash
sudo sed -ri 's/^\s*max_log_file_action\s*=.*/max_log_file_action = keep_logs/' /etc/audit/auditd.conf
```

---

## 🔍 แยกคำสั่งทีละส่วน

| ส่วน | ความหมาย |
|---|---|
| `sudo` | รันคำสั่งด้วยสิทธิ์ Root |
| `sed` | โปรแกรมสำหรับค้นหาและแก้ไขข้อความในไฟล์ |
| `-r` | ใช้ Regular Expression แบบ Extended |
| `-i` | แก้ไขไฟล์โดยตรง (in-place) |
| `/etc/audit/auditd.conf` | ไฟล์ Configuration ของ `auditd` |
| `s/.../.../` | คำสั่ง Substitute ของ `sed` |

---

## 🧩 วิเคราะห์ Regular Expression

ส่วนนี้คือสิ่งที่ `sed` ใช้ค้นหา:

```text
^\s*max_log_file_action\s*=.*
```

ความหมาย:

- `^` → เริ่มต้นบรรทัด
- `\s*` → มีช่องว่างก่อน `max_log_file_action` ได้ 0 ตัวหรือหลายตัว
- `max_log_file_action` → ค้นหาชื่อ Parameter นี้
- `\s*` → รองรับช่องว่างก่อน `=`
- `=` → เครื่องหมายเท่ากับ
- `.*` → ข้อความใด ๆ ที่เหลือในบรรทัด

ดังนั้นสามารถจับได้ทั้ง:

```text
max_log_file_action = rotate
```

หรือ

```text
max_log_file_action=rotate
```

หรือกรณีที่มีช่องว่างด้านหน้า:

```text
    max_log_file_action = rotate
```

แล้วแทนที่ทั้งบรรทัดด้วย:

```text
max_log_file_action = keep_logs
```

---

## 🔐 `keep_logs` คืออะไร?

ใน `auditd` ค่า:

```text
max_log_file_action = keep_logs
```

หมายถึง เมื่อ `audit.log` ถึงขนาดที่กำหนดโดย:

```text
max_log_file = ...
```

ให้ **เก็บไฟล์ Log เดิมไว้** และไม่ดำเนินการหมุน Log (`rotate`) ตามกลไกของ `auditd`

ตัวอย่าง Audit Log:

```text
/var/log/audit/audit.log
```

เมื่อไฟล์ถึงขนาดที่กำหนด ระบบจะไม่หมุนและลบ Log เก่าตามการตั้งค่า `rotate` แต่จะคงไฟล์ Log ไว้

---

## ⚠️ จุดที่ต้องระวัง

`keep_logs` ช่วยป้องกันการสูญเสีย Audit Log แต่มีข้อเสียคือ **พื้นที่ `/var/log/audit` อาจเต็มได้** หากไม่มีระบบจัดการ Log ภายนอก เช่น:

- Graylog
- Wazuh
- Log Archive
- Centralized Log Server

ดังนั้นควรวางแผนเรื่อง:

**Log Retention + Storage Capacity**

ควบคู่กัน

---

## 🔎 ตรวจสอบค่าที่ตั้งไว้

ตรวจสอบค่า `max_log_file` และ `max_log_file_action`:

```bash
sudo grep -E '^(max_log_file|max_log_file_action)' /etc/audit/auditd.conf
```

ตัวอย่างผลลัพธ์:

```text
max_log_file = 50
max_log_file_action = keep_logs
```

---

## 💾 ตรวจสอบพื้นที่จัดเก็บ Audit Log

ตรวจสอบพื้นที่ของ `/var/log/audit`:

```bash
df -h /var/log/audit
```

ตัวอย่าง:

```text
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3        20G   8G   12G  40% /var/log
```

---

## 🛡️ ประโยชน์ด้าน Linux Security Hardening

การกำหนด:

```text
max_log_file_action = keep_logs
```

มีประโยชน์ในการลดความเสี่ยงที่ **Audit Log สำคัญจะถูกหมุนและสูญหาย**

Audit Log มีความสำคัญต่อ:

- Security Monitoring
- Threat Hunting
- Incident Response
- Forensic Investigation
- Compliance
- การตรวจสอบกิจกรรมของผู้ใช้และระบบ

อย่างไรก็ตาม `keep_logs` ไม่ควรใช้งานโดยไม่คำนึงถึงพื้นที่จัดเก็บ เพราะ Audit Log สามารถเพิ่มขึ้นเรื่อย ๆ จนทำให้ Filesystem เต็มได้

---

## 📌 สรุป

```text
max_log_file_action = keep_logs
```

หมายถึง:

> เมื่อ Audit Log ถึงขนาดที่กำหนด ให้เก็บ Log ไว้แทนการ Rotate ตามปกติ

เหมาะกับระบบที่ต้องการ **รักษา Audit Evidence** และมีการจัดการพื้นที่หรือส่ง Log ไปยังระบบ Centralized Logging เช่น **Graylog หรือ Wazuh**

ควรตรวจสอบทั้ง:

```bash
sudo grep -E '^(max_log_file|max_log_file_action)' /etc/audit/auditd.conf
```

และ:

```bash
df -h /var/log/audit
```

เพื่อให้แน่ใจว่า Audit Log จะไม่ทำให้พื้นที่ Disk เต็ม
