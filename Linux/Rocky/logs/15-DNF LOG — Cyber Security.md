# 🔐 DNF LOG — Cyber Security / Threat Hunting

## 1. ดู DNF Log ล่าสุด

```bash
sudo tail -n 100 /var/log/dnf.log
```

## 2. ดูแบบต่อเนื่อง (Real-time)

```bash
sudo tail -f /var/log/dnf.log
```

## 3. ดูเฉพาะรายการติดตั้ง Package

```bash
sudo grep -Ei 'install|installed' /var/log/dnf.log
```

## 4. ดูเฉพาะรายการลบ Package

```bash
sudo grep -Ei 'remove|removed|erase|erased' /var/log/dnf.log
```

## 5. ดูเฉพาะรายการ Upgrade / Update

```bash
sudo grep -Ei 'upgrade|updated|update' /var/log/dnf.log
```

## 6. ค้นหา Package ที่เกี่ยวข้องกับเครื่องมือที่น่าสงสัย

```bash
sudo grep -Ei \
'(nmap|nc|netcat|socat|tcpdump|wireshark|hydra|john|hashcat|nikto|sqlmap|masscan)' \
/var/log/dnf.log
```

## 7. ค้นหา Package ที่เกี่ยวข้องกับ Remote Access

```bash
sudo grep -Ei \
'(openssh|telnet|rsh|rlogin|xinetd|ftp|vsftpd)' \
/var/log/dnf.log
```

## 8. ค้นหา Package ที่เกี่ยวข้องกับ Privilege / Security

```bash
sudo grep -Ei \
'(sudo|polkit|selinux|audit|libcap|setroubleshoot)' \
/var/log/dnf.log
```

## 9. ดูช่วงเวลาที่มีการเปลี่ยนแปลง Package

```bash
sudo grep -E \
'2026-09-(15|16)' \
/var/log/dnf.log
```

## 10. ค้นหาการติดตั้งในช่วงเวลาที่ต้องการ

```bash
sudo grep -Ei \
'2026-09-16.*(install|installed)' \
/var/log/dnf.log
```

---

# 🕵️ Threat Hunting

## 11. ค้นหา Package ที่ติดตั้งแบบผิดปกติ

```bash
sudo grep -Ei \
'(install|installed)' \
/var/log/dnf.log | tail -n 50
```

## 12. ค้นหา Package ที่ถูก Downgrade

```bash
sudo grep -Ei 'downgrade|downgraded' /var/log/dnf.log
```

## 13. ค้นหาการลบ Package Security

```bash
sudo grep -Ei \
'(remove|erase).*(audit|selinux|firewalld|openssh|sudo|rsyslog)' \
/var/log/dnf.log
```

## 14. ค้นหา Repository ที่มีการใช้งาน

```bash
sudo grep -Ei \
'(repo|repository)' \
/var/log/dnf.log
```

## 15. ดูประวัติ DNF แบบเป็น Transaction

```bash
sudo dnf history
```

## 16. ดูรายละเอียด Transaction

```bash
sudo dnf history info <TRANSACTION_ID>
```

### ตัวอย่าง

```bash
sudo dnf history info 25
```

## 17. แสดงรายการ Package ที่ถูกติดตั้งใน Transaction

```bash
sudo dnf history info <TRANSACTION_ID> | \
grep -Ei 'Install|Upgrade|Downgrade|Erase'
```

---

# 🚨 สิ่งที่ควรสงสัย

## Package ถูกติดตั้งในเวลาที่ไม่มี Maintenance

```bash
sudo grep -Ei 'install|installed' /var/log/dnf.log
```

## มีการติดตั้ง Network Tool

```bash
sudo grep -Ei \
'(nmap|netcat|nc|socat|masscan|tcpdump)' \
/var/log/dnf.log
```

## มีการติดตั้ง Remote Access Tool

```bash
sudo grep -Ei \
'(telnet|rsh|rlogin|vsftpd|openssh-server)' \
/var/log/dnf.log
```

## มีการลบ Security Component

```bash
sudo grep -Ei \
'(remove|erase).*(firewalld|audit|selinux|sudo)' \
/var/log/dnf.log
```

---

# 🔎 Correlation กับ Log อื่น

## ตรวจสอบว่าใครเป็นผู้ใช้ sudo ในช่วงเวลาเดียวกัน

```bash
sudo journalctl _COMM=sudo \
--since "2026-09-16 08:00:00" \
--until "2026-09-16 12:00:00" \
--no-pager
```

## ตรวจสอบ SSH Login

```bash
sudo journalctl -u sshd \
--since "2026-09-16 08:00:00" \
--until "2026-09-16 12:00:00" \
--no-pager
```

## ตรวจสอบ Audit Log

```bash
sudo ausearch -ts 09:00:00 -te 12:00:00 -m USER_CMD,EXECVE \
-i
```

## ตรวจสอบ Process ที่เกี่ยวข้อง

```bash
sudo journalctl \
--since "2026-09-16 08:00:00" \
--until "2026-09-16 12:00:00" \
--no-pager | \
grep -Ei '(dnf|rpm|sudo|ssh|install|remove)'
```

---

# 🧠 Security Concept

DNF Log สามารถใช้เป็นหนึ่งในแหล่งข้อมูลสำหรับตรวจสอบกิจกรรมที่เกี่ยวข้องกับ Attack Chain:

```text
Initial Access
      ↓
SSH Login
      ↓
Privilege Escalation / sudo
      ↓
DNF Install
      ↓
Tool Installation
      ↓
Persistence / Lateral Movement
```

## ตัวอย่าง Attack Chain — Network Reconnaissance

```text
ssh login
    ↓
sudo -i
    ↓
dnf install nmap
    ↓
network reconnaissance
```

## ตัวอย่าง Attack Chain — Reverse Shell / Tunneling

```text
ssh login
    ↓
sudo -i
    ↓
dnf install socat
    ↓
reverse shell / tunneling
```

> ⚠️ DNF Log ไม่ควรถูกวิเคราะห์แบบแยกเดี่ยว เพราะ DNF Log สามารถบอกได้ว่า Package ถูกเปลี่ยนแปลง แต่ไม่ได้ให้บริบททั้งหมดว่า **ใครเป็นผู้ดำเนินการ, เข้ามาจากไหน และ Process ใดเป็นผู้เรียกใช้งาน**

ดังนั้นควร Correlate ข้อมูลกับ Log และ Security Telemetry แหล่งอื่น

```text
                    ┌─────────────────────┐
                    │      DNF Log        │
                    │ Package Changes     │
                    └──────────┬──────────┘
                               │
              ┌────────────────┼────────────────┐
              ↓                ↓                ↓
       /var/log/secure   audit/audit.log    journalctl
              │                │                │
              └────────────────┼────────────────┘
                               ↓
                         Wazuh / Graylog
                               │
                               ↓
                     EDR / Tetragon / Falco
                               │
                               ↓
                       Threat Correlation
                               │
                               ↓
                         Attack Chain
```

---

# 🛡️ Log Sources ที่ควร Correlate

## `/var/log/secure`

ใช้ตรวจสอบ:

- SSH Authentication
- Successful Login
- Failed Login
- sudo
- PAM Events

## `/var/log/audit/audit.log`

ใช้ตรวจสอบ:

- User Command
- EXECVE
- Privilege Changes
- System Calls
- Process Execution
- File / Configuration Changes

## `journalctl`

ใช้ตรวจสอบ:

- Service Events
- Process Events
- Authentication
- System Events
- Application Logs

## Wazuh

ใช้สำหรับ:

- SIEM Correlation
- Rule-based Detection
- Alerting
- Threat Hunting
- MITRE ATT&CK Mapping

## Graylog

ใช้สำหรับ:

- Centralized Log Management
- Search
- Correlation
- Visualization
- Threat Hunting

## EDR / Tetragon / Falco

ใช้ตรวจสอบ:

- Process Behavior
- Runtime Activity
- File Access
- Network Activity
- Container Activity
- eBPF-based Security Events

---

# 🎯 Threat Hunting Perspective

DNF Log เพียงอย่างเดียวอาจตอบได้ว่า:

```text
มี Package ถูกติดตั้งหรือไม่?
มี Package ถูกลบหรือไม่?
มี Package ถูก Upgrade หรือ Downgrade หรือไม่?
```

แต่เมื่อ Correlate กับ Security Telemetry จะสามารถตอบคำถามเพิ่มเติมได้ เช่น:

```text
ใครเป็นผู้ติดตั้ง?
        ↓
Login มาจาก IP ไหน?
        ↓
มีการใช้ sudo หรือไม่?
        ↓
Process ใดเรียก dnf?
        ↓
ติดตั้ง Package อะไร?
        ↓
หลังจากติดตั้งแล้วมี Process อะไรเกิดขึ้น?
        ↓
มี Network Connection ใหม่หรือไม่?
        ↓
มี File / Configuration ถูกเปลี่ยนแปลงหรือไม่?
        ↓
มี Persistence หรือ Lateral Movement หรือไม่?
```

---

# 🔐 Key Security Principle

> **DNF Log = Evidence ของ Package Management Activity**

แต่การตรวจจับ Attack Chain ที่สมบูรณ์ควรใช้การ **Correlation หลายแหล่ง (Multi-Source Correlation)**

```text
SSH
 ↓
Authentication Log
 ↓
sudo
 ↓
Audit / EXECVE
 ↓
DNF
 ↓
Package Installation
 ↓
Process Execution
 ↓
Network Activity
 ↓
File / Configuration Changes
 ↓
Wazuh / Graylog / EDR
 ↓
Threat Detection
```

ดังนั้น DNF Log จึงไม่ใช่เพียง Log สำหรับตรวจสอบการติดตั้ง Software แต่สามารถเป็น **หนึ่งใน Evidence สำคัญของ Threat Hunting บน Linux** เมื่อถูกนำไป Correlate กับ Authentication, Audit, Process, Network และ Endpoint Telemetry
