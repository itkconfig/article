# เปรียบเทียบสถาปัตยกรรมและการใช้งาน: iptables vs nftables ใน Rocky Linux

บทความนี้เรียบเรียงขึ้นเพื่อเปรียบเทียบความแตกต่างทางสถาปัตยกรรม การทำงาน และแนวทางปฏิบัติตามมาตรฐานระหว่าง **iptables** และ **nftables** บนระบบปฏิบัติการ **Rocky Linux** โดยอ้างอิงแนวทางของ Red Hat Enterprise Linux

---

## 1. บทนำและสถานะปัจจุบันใน Rocky Linux

ระบบการกรองแพ็กเก็ต (Packet Filtering) และไฟร์วอลล์ใน Linux ได้ก้าวเข้าสู่ยุคใหม่ด้วยการนำ **nftables** มาใช้เป็นเฟรมเวิร์กหลักในระดับเคอร์เนล แทนแนวทาง iptables แบบดั้งเดิม

### Rocky Linux 8 และ 9+

ใน Rocky Linux ความแตกต่างระหว่าง `iptables` และ `nftables` ไม่ได้หมายถึงการเลือกใช้เครื่องมือที่แยกจากกันโดยสิ้นเชิง

- **nftables** เป็นเฟรมเวิร์กหลักที่ใช้ `nf_tables` ใน Linux kernel
- คำสั่ง `iptables` ในระบบสมัยใหม่สามารถทำงานผ่าน **iptables-nft compatibility layer**
- `iptables-nft` ยอมรับไวยากรณ์แบบ iptables ดั้งเดิม แล้วแปลงคำสั่งไปทำงานผ่าน `nf_tables`
- สำหรับระบบใหม่ ควรพิจารณาใช้ **native nftables** หรือ **firewalld** ตามลักษณะงาน

> **แนวคิดสำคัญ:**  
> บน Rocky Linux รุ่นใหม่ คำว่า `iptables` ไม่ได้หมายความว่าระบบกำลังใช้ legacy iptables kernel backend เสมอไป

---

## 2. เปรียบเทียบสถาปัตยกรรมและฟีเจอร์หลัก

### 2.1 สถาปัตยกรรมเคอร์เนลและ Compatibility Layer

#### iptables

ในอดีต `iptables` ทำงานร่วมกับ kernel framework แบบดั้งเดิม เช่น `ip_tables`

แต่บนระบบ Linux รุ่นใหม่ อาจพบว่า `iptables` ทำงานผ่าน `iptables-nft` ซึ่งเป็น compatibility layer ที่แปลงคำสั่ง iptables ไปเป็นกฎของ `nf_tables`

แนวคิดโดยย่อ:

    iptables command
          │
          ▼
      iptables-nft
          │
          ▼
       nf_tables
          │
          ▼
      Linux Kernel

#### nftables

`nftables` ใช้ framework `nf_tables` โดยตรง

    nft command
          │
          ▼
       nf_tables
          │
          ▼
      Linux Kernel

ดังนั้น หากเริ่มออกแบบ firewall configuration ใหม่ การใช้ syntax ของ `nft` โดยตรงจะช่วยให้สามารถใช้ความสามารถของ nftables ได้ครบถ้วนมากกว่า compatibility layer

---

## 3. Tables และ Chains

### iptables

iptables มีโครงสร้างตารางที่เป็นที่รู้จัก เช่น

    filter
    nat
    mangle
    raw
    security

แต่ละตารางมี chain ที่เกี่ยวข้อง เช่น

    INPUT
    OUTPUT
    FORWARD
    PREROUTING
    POSTROUTING

ตัวอย่าง:

    iptables -L -n -v

---

### nftables

nftables ไม่มีชุด tables/chains แบบเดียวกับ iptables ที่ถูกสร้างขึ้นมาให้โดยอัตโนมัติทั้งหมด

ผู้ดูแลระบบสามารถสร้างเฉพาะโครงสร้างที่ต้องการใช้งาน เช่น

    nft add table inet filter
    nft add chain inet filter input

ตรวจสอบ configuration:

    nft list ruleset

แนวคิด:

    nftables
    │
    ├── table inet filter
    │   │
    │   ├── chain input
    │   ├── chain output
    │   └── chain forward
    │
    └── table inet nat
        │
        ├── chain prerouting
        └── chain postrouting

---

## 4. การรองรับ IPv4 และ IPv6

### iptables

โดยทั่วไปต้องใช้เครื่องมือแยกกัน

    iptables
    ip6tables

ตัวอย่าง IPv4:

    iptables -A INPUT -p tcp --dport 22 -j ACCEPT

ตัวอย่าง IPv6:

    ip6tables -A INPUT -p tcp --dport 22 -j ACCEPT

---

### nftables

nftables สามารถใช้ตารางประเภท `inet` เพื่อจัดการ IPv4 และ IPv6 ภายใน ruleset เดียวกันได้

ตัวอย่าง:

    nft add table inet filter

จากนั้นสามารถสร้าง chain:

    nft add chain inet filter input

และเพิ่ม rule:

    nft add rule inet filter input tcp dport 22 accept

แนวคิด:

    inet
     │
     ├── IPv4
     │
     └── IPv6
           │
           ▼
       Same Ruleset

---

## 5. Syntax และการจัดการ Rules

### iptables

ตัวอย่างการอนุญาต SSH:

    iptables -A INPUT -p tcp --dport 22 -j ACCEPT

โครงสร้างโดยทั่วไป:

    -A INPUT
    │
    ├── -p tcp
    ├── --dport 22
    └── -j ACCEPT

---

### nftables

คำสั่งเดียวกันในรูปแบบ nftables:

    nft add rule inet filter input tcp dport 22 accept

ตัวอย่าง rule ที่มีหลายองค์ประกอบ:

    nft add rule inet filter input tcp dport 22 counter log prefix "SSH: " accept

Rule เดียวสามารถประกอบด้วย:

    Match
      │
      ├── tcp
      └── dport 22
           │
           ▼
        Counter
           │
           ▼
          Log
           │
           ▼
         Accept

---

## 6. Sets และ Maps

หนึ่งในความสามารถสำคัญของ nftables คือ **Sets** และ **Maps**

### iptables

หากต้องจัดการ IP จำนวนมาก มักใช้ `ipset` ร่วมกับ iptables

ตัวอย่าง:

    ipset create trusted_ips hash:ip
    ipset add trusted_ips 192.168.1.10
    ipset add trusted_ips 192.168.1.20

จากนั้นใช้กับ iptables:

    iptables -A INPUT -m set --match-set trusted_ips src -j ACCEPT

---

### nftables

nftables มี Sets เป็นส่วนหนึ่งของระบบโดยตรง

ตัวอย่าง:

    nft add rule inet filter input ip saddr { 192.168.1.10, 192.168.1.20 } accept

สามารถใช้กับ Port ได้เช่นกัน:

    nft add rule inet filter input tcp dport { 22, 80, 443 } accept

ทำให้สามารถเขียนกฎแบบรวมหลายค่าภายใน rule เดียวได้

---

# 7. Comparison Matrix

| มิติเปรียบเทียบ | iptables / iptables-nft | nftables |
|---|---|---|
| แนวทาง | Compatibility / legacy syntax | Native Linux packet-filtering framework |
| Kernel framework | สามารถทำงานผ่าน `nf_tables` เมื่อใช้ iptables-nft | `nf_tables` |
| Syntax | รูปแบบดั้งเดิม | Syntax ของ nft |
| IPv4 / IPv6 | `iptables` / `ip6tables` แยกกัน | ใช้ `inet` รวม IPv4 + IPv6 ได้ |
| Sets | มักใช้ `ipset` | Built-in Sets |
| Maps | จำกัดกว่า | Built-in Maps |
| Counter | รองรับ | รองรับ |
| Logging | รองรับ | รองรับ |
| Atomic ruleset management | จำกัดกว่า | รองรับได้ดี |
| เหมาะกับระบบใหม่ | ใช้เพื่อ compatibility | เหมาะสำหรับ native configuration |
| การจัดการระดับสูง | firewalld สามารถจัดการ backend ให้ | firewalld สามารถใช้ nftables backend |

---

# 8. ตรวจสอบว่า Rocky Linux ใช้ Backend อะไร

ตรวจสอบเวอร์ชันของ iptables:

    iptables --version

ตัวอย่าง:

    iptables v1.8.x (nf_tables)

หากพบ:

    (nf_tables)

หมายความว่า `iptables` กำลังทำงานผ่าน nftables backend

สามารถตรวจสอบตำแหน่งคำสั่งเพิ่มเติม:

    command -v iptables

ตรวจสอบ symbolic link:

    readlink -f "$(command -v iptables)"

ตรวจสอบ nftables โดยตรง:

    nft --version

และดู ruleset:

    sudo nft list ruleset

---

# 9. iptables-translate

หากมีระบบเดิมที่ใช้ iptables และต้องการเริ่มย้ายไป nftables สามารถใช้:

    iptables-translate -A INPUT -p tcp --dport 22 -j ACCEPT

ตัวอย่างผลลัพธ์:

    nft add rule ip filter INPUT tcp dport 22 counter accept

เครื่องมือนี้มีประโยชน์สำหรับการเรียนรู้ syntax และช่วยแปลง rule แบบเดิมไปเป็น syntax ของ nftables

สามารถแปลง IPv6 ได้ด้วย:

    ip6tables-translate -A INPUT -p tcp --dport 22 -j ACCEPT

---

# 10. ข้อควรระวังในการใช้งาน

## 10.1 หลีกเลี่ยงการบริหาร Ruleset เดียวกันด้วยหลายเครื่องมือ

ไม่ควรสร้างระบบที่ผู้ดูแลระบบคนหนึ่งแก้ไข firewall ด้วย:

    iptables

ขณะที่อีกส่วนหนึ่งแก้ไขด้วย:

    nft

และอีกส่วนหนึ่งใช้:

    firewall-cmd

กับ ruleset เดียวกันโดยไม่มีการออกแบบที่ชัดเจน

แม้ `iptables-nft` จะใช้ `nf_tables` อยู่เบื้องหลัง แต่แต่ละเครื่องมือมี abstraction และรูปแบบการจัดการ configuration ที่แตกต่างกัน

จึงอาจทำให้เกิด:

    Configuration
        │
        ├── iptables-nft
        │
        ├── nft
        │
        └── firewalld
              │
              ▼
          Confusion
          Duplicate Rules
          Unexpected Behavior

ควรกำหนดให้ชัดเจนว่าเครื่องนั้นใช้เครื่องมือใดเป็น **source of truth**

---

# 11. firewalld บน Rocky Linux

สำหรับเครื่อง Rocky Linux ที่ต้องการ firewall management ระดับระบบ โดยทั่วไปสามารถใช้:

    firewall-cmd

ตรวจสอบสถานะ:

    sudo firewall-cmd --state

ดู zone:

    sudo firewall-cmd --get-active-zones

ดู configuration:

    sudo firewall-cmd --list-all

ตรวจสอบ backend ของ firewalld:

    sudo grep -E '^FirewallBackend=' /etc/firewalld/firewalld.conf

ตัวอย่าง:

    FirewallBackend=nftables

แนวคิดการจัดการ:

    Administrator
          │
          ▼
      firewall-cmd
          │
          ▼
       firewalld
          │
          ▼
       nftables
          │
          ▼
       nf_tables
          │
          ▼
      Linux Kernel

---

# 12. ควรเลือกใช้อะไรบน Rocky Linux?

## ระบบใหม่

หากต้องการสร้าง firewall ruleset ใหม่แบบ native:

    nftables

เป็นแนวทางที่ตรงกับ architecture สมัยใหม่ของ Linux

---

## ระบบที่ต้องการบริหารง่าย

หากต้องการจัดการ firewall ผ่าน zone, service, port และ interface:

    firewalld

มักเหมาะกับการบริหารระบบทั่วไปมากกว่า เพราะมี abstraction ระดับสูงให้ใช้งาน

---

## ระบบเดิมที่ใช้ iptables

หากระบบเดิมใช้:

    iptables

และพบว่า:

    iptables v1.8.x (nf_tables)

ไม่จำเป็นต้องรีบเปลี่ยนเพียงเพราะชื่อคำสั่งเป็น iptables

ควรตรวจสอบก่อนว่า application, script และ automation เดิมพึ่งพา syntax หรือ behavior ใดอยู่

---

# 13. แนวทาง Migration

สามารถวางแผน migration ได้ดังนี้:

    Existing Server
          │
          ▼
    ตรวจสอบ iptables
          │
          ├── Legacy backend
          │
          └── nf_tables backend
                   │
                   ▼
             ตรวจสอบ Rules
                   │
                   ▼
           iptables-translate
                   │
                   ▼
            ทดสอบ nftables
                   │
                   ▼
          ตรวจสอบ Application
                   │
                   ▼
          ตรวจสอบ Network Flow
                   │
                   ▼
          Production Migration

ตรวจสอบ rules เดิม:

    sudo iptables-save

ตรวจสอบ nftables:

    sudo nft list ruleset

แปลง rule:

    iptables-translate <iptables-rule>

ทดสอบ connectivity ก่อน migration จริง เช่น:

    ss -lntup

และ:

    sudo firewall-cmd --list-all

รวมถึงตรวจสอบ network traffic ด้วย:

    sudo tcpdump -ni any port 22

---

# 14. สรุป

    iptables
       │
       ├── Legacy syntax
       │
       ├── iptables-nft compatibility layer
       │
       └── สามารถทำงานบน nf_tables
                  │
                  ▼
            Linux Kernel


    nftables
       │
       ├── Native syntax
       ├── Tables / Chains
       ├── Sets / Maps
       ├── IPv4 + IPv6 ผ่าน inet
       ├── Counter / Logging
       └── nf_tables
                  │
                  ▼
            Linux Kernel


    firewalld
       │
       ├── High-level firewall management
       ├── Zones
       ├── Services
       ├── Ports
       └── Backend
             │
             ▼
          nftables

---

# Key Takeaways

- **nftables** เป็น framework สมัยใหม่ของ Linux kernel สำหรับ packet filtering
- `iptables` บน Rocky Linux รุ่นใหม่อาจทำงานผ่าน **iptables-nft**
- `(nf_tables)` ใน `iptables --version` เป็นตัวบ่งชี้ว่า iptables กำลังใช้ nftables backend
- `nftables` รองรับ **Sets, Maps, inet family, counters และ logging** ในตัว
- สำหรับระบบใหม่ ควรพิจารณา **native nftables** หรือ **firewalld** ตามระดับการควบคุมที่ต้องการ
- สำหรับระบบที่ใช้ iptables เดิม ควรตรวจสอบ backend และ dependencies ก่อน migration
- ไม่ควรให้ `iptables`, `nft` และ `firewalld` ต่างคนต่างเป็นผู้ควบคุม ruleset เดียวกันโดยไม่มีการออกแบบที่ชัดเจน
- ก่อนเปลี่ยน firewall บน Production ควรทดสอบ SSH, Application, Routing, NAT และ Network Services ให้ครบถ้วน

> **Security Best Practice:**  
> ก่อนแก้ไข firewall ผ่าน SSH ควรมี console access หรือ out-of-band access สำรองไว้เสมอ เพื่อป้องกันการ Lockout ตัวเองจากระบบ

---

# Rocky Linux Firewall Architecture

    Rocky Linux
         │
    ┌────┴────┐
    │         │
    ▼         ▼
firewalld   Native nft
    │         │
    └────┬────┘
         │
      nftables
         │
      nf_tables
         │
    Linux Kernel
         │
    Network Stack
         │
      NIC / Traffic
