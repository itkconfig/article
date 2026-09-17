# 🔐 MAN SECTION — SOC / CYBERSECURITY ADMIN
# Rocky Linux

## 🧠 หลักคิดจำง่าย

ถามตัวเองก่อนว่า:

> "สิ่งที่เรากำลังค้นหา เป็น คำสั่ง, Config/File Format หรือ Concept/Protocol?"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 1️⃣ SECTION 1 — User Commands
# คำสั่งที่ Admin / SOC ใช้งานโดยตรง

man 1 ls
man 1 ps
man 1 ss
man 1 ip
man 1 journalctl
man 1 systemctl
man 1 firewall-cmd
man 1 ssh
man 1 find
man 1 grep
man 1 awk

## 🔎 ตัวอย่างงาน SOC

# ตรวจสอบ Process
man 1 ps

ps aux
ps -ef

# ตรวจสอบ Network Connection
man 1 ss

ss -tulnp
ss -antp

# ตรวจสอบ Log
man 1 journalctl

journalctl -u sshd
journalctl -p warning
journalctl --since today

# ตรวจสอบ Service
man 1 systemctl

systemctl status sshd
systemctl list-units --type=service

# ตรวจสอบ Firewall
man 1 firewall-cmd

firewall-cmd --list-all
firewall-cmd --list-ports

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 2️⃣ SECTION 5 — File Formats / Configuration
# ไฟล์ Config และรูปแบบไฟล์ระบบ

man 5 passwd
man 5 shadow
man 5 ssh_config
man 5 sudoers
man 5 auditd.conf
man 5 rsyslog.conf
man 5 journald.conf
man 5 fstab
man 5 hosts
man 5 protocols

## 🔎 ตัวอย่างงาน SOC

# SSH Client Configuration
man 5 ssh_config

# SSH Server Configuration
man 5 sshd_config

# sudo Policy
man 5 sudoers

# Auditd Configuration
man 5 auditd.conf

# rsyslog Configuration
man 5 rsyslog.conf

# System Mount Configuration
man 5 fstab

# ตรวจสอบ Account File
man 5 passwd
man 5 shadow

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 3️⃣ SECTION 7 — Concepts / Protocols / Miscellaneous
# Concept สำคัญของ Linux / Network / Security

man 7 tcp
man 7 udp
man 7 ip
man 7 socket
man 7 signal
man 7 capabilities

## 🔎 ตัวอย่างงาน SOC

# เข้าใจ TCP
man 7 tcp

# เข้าใจ UDP
man 7 udp

# เข้าใจ IP
man 7 ip

# เข้าใจ Socket
man 7 socket

# เข้าใจ Linux Signals
man 7 signal

# เข้าใจ Linux Capabilities
man 7 capabilities

ตัวอย่าง:

CAP_NET_ADMIN
CAP_NET_RAW
CAP_SYS_ADMIN

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 4️⃣ SECTION 8 — System Administration
# คำสั่งสำหรับ System Administrator

man 8 auditctl
man 8 ausearch
man 8 aureport
man 8 semanage
man 8 restorecon
man 8 setsebool
man 8 useradd
man 8 usermod
man 8 firewall-cmd

## 🔎 ตัวอย่างงาน SOC

# Audit Rule
man 8 auditctl

auditctl -l

# ค้นหา Audit Event
man 8 ausearch

ausearch -m USER_LOGIN
ausearch -m EXECVE

# สรุป Audit Report
man 8 aureport

aureport -au
aureport -x

# SELinux
man 8 semanage
man 8 restorecon
man 8 setsebool

getenforce
semanage fcontext -l
restorecon -Rv /var/www

# Account Administration
man 8 useradd
man 8 usermod

usermod -L username
usermod -aG wheel username

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 SOC QUICK LOOKUP

## 🟢 Process / Malware Hunting

man 1 ps
man 1 pgrep
man 1 pstree
man 1 lsof

ps aux
pgrep -a ssh
lsof -i
lsof -p PID

## 🔵 Network Hunting

man 1 ss
man 1 ip
man 7 tcp
man 7 socket

ss -tulnp
ss -antp
ip addr
ip route

## 🟡 Log Hunting

man 1 journalctl
man 1 logger

journalctl -u sshd
journalctl --since today
journalctl -p err

## 🔴 Authentication / Account

man 5 passwd
man 5 shadow
man 5 sudoers

grep "Failed password" /var/log/secure
grep "Accepted" /var/log/secure

## 🟣 Auditd

man 8 auditctl
man 8 ausearch
man 8 aureport
man 5 auditd.conf

auditctl -l
ausearch -m USER_LOGIN
ausearch -m EXECVE
aureport -au

## 🟠 SELinux

man 8 semanage
man 8 restorecon
man 8 setsebool
man 7 capabilities

getenforce
semanage fcontext -l
restorecon -Rv /path

## 🔥 Firewall

man 1 firewall-cmd

firewall-cmd --state
firewall-cmd --list-all
firewall-cmd --list-services
firewall-cmd --list-ports

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧩 วิธีเลือก Section แบบเร็ว

"เป็นคำสั่ง?"
        ↓
    SECTION 1
        ↓
man 1 ps
man 1 ss
man 1 journalctl

"เป็น Config / File Format?"
        ↓
    SECTION 5
        ↓
man 5 sudoers
man 5 sshd_config
man 5 auditd.conf

"เป็น Concept / Protocol?"
        ↓
    SECTION 7
        ↓
man 7 tcp
man 7 socket
man 7 capabilities

"เป็นคำสั่ง Admin?"
        ↓
    SECTION 8
        ↓
man 8 auditctl
man 8 ausearch
man 8 semanage

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 เทคนิคสำคัญ

## ถ้าไม่รู้ว่าอยู่ Section ไหน

man -f command

หรือ

whatis command

ตัวอย่าง:

man -f passwd

# อาจพบหลาย Section เช่น
# passwd (1)
# passwd (5)

จึงสามารถระบุ Section ได้:

man 1 passwd
man 5 passwd

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔐 SOC Mindset

man
 ↓
ค้นหาความหมายของ Command
 ↓
เข้าใจ Option
 ↓
ทดลอง Command
 ↓
ตรวจสอบ Log
 ↓
Correlate Event
 ↓
Threat Hunting
 ↓
Incident Response

# 🎯 จำง่าย

1 = COMMAND
5 = CONFIG / FILE
7 = CONCEPT / PROTOCOL
8 = ADMIN

> SOC Admin ไม่จำเป็นต้องจำทุก Command
> แต่ควรรู้ว่า "จะค้นคู่มือจาก Section ไหน"
