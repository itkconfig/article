# 🔎 อธิบายคำสั่ง `dig +short -x 192.168.1.51 @127.0.0.1`

คำสั่งนี้ใช้สำหรับ **ทดสอบ Reverse DNS Lookup** หรือการค้นหาว่า IP Address `192.168.1.51` ถูกกำหนดให้ตรงกับชื่อ Hostname ใด โดยบังคับให้ Query ไปยัง DNS Server ที่ทำงานอยู่บนเครื่องตัวเอง (`127.0.0.1`)

```text
dig +short -x 192.168.1.51 @127.0.0.1
```

## 🧩 แยกความหมายแต่ละส่วน

### `dig`

เครื่องมือสำหรับส่ง DNS Query และตรวจสอบการทำงานของ DNS Server บน Linux

ใช้ตรวจสอบได้ทั้ง

* Forward DNS → ชื่อ → IP
* Reverse DNS → IP → ชื่อ
* A / AAAA
* PTR
* MX
* NS
* และ DNS Record ประเภทอื่น ๆ

---

### `+short`

กำหนดให้ `dig` แสดงผลแบบสั้น

แทนที่จะแสดงข้อมูล DNS ทั้งหมด เช่น

* Header
* Query
* Answer
* Authority
* Additional
* Query time
* Server ที่ตอบคำถาม

จะแสดงเฉพาะคำตอบที่สำคัญ

ตัวอย่าง:

```text
ipa-replica.lab.lan.
```

---

### `-x 192.168.1.51`

`-x` หมายถึง **Reverse DNS Lookup**

คือการถามว่า

```text
192.168.1.51
     ↓
ชื่อ Hostname อะไร?
```

เบื้องหลัง `dig` จะเปลี่ยน IP:

```text
192.168.1.51
```

เป็น Reverse DNS Name:

```text
51.1.168.192.in-addr.arpa.
```

แล้ว Query DNS Record ประเภท `PTR`

แนวคิดคือ:

```text
Forward DNS

ipa-replica.lab.lan
        ↓
192.168.1.51
```

ส่วน Reverse DNS คือ:

```text
192.168.1.51
        ↓
ipa-replica.lab.lan
```

---

### `@127.0.0.1`

ส่วนนี้สำคัญมาก

```text
@127.0.0.1
```

หมายถึง

> ให้ส่ง DNS Query ไปยัง DNS Server ที่ IP `127.0.0.1`

หรือก็คือ DNS Server ที่กำลังทำงานอยู่บนเครื่องเดียวกัน

แทนที่จะใช้ DNS Server ที่ถูกกำหนดไว้ใน:

```text
/etc/resolv.conf
```

จึงเหมาะสำหรับทดสอบว่า **DNS Server ภายในเครื่องทำงานและมี Record ที่ต้องการหรือไม่**

---

# 🔄 การทำงานของคำสั่ง

เมื่อรัน:

```text
dig +short -x 192.168.1.51 @127.0.0.1
```

สามารถมองภาพการทำงานได้ดังนี้:

```text
dig
 │
 ├── Reverse Lookup (-x)
 │
 ├── IP: 192.168.1.51
 │
 ├── แปลงเป็น:
 │      51.1.168.192.in-addr.arpa.
 │
 ├── Query:
 │      PTR
 │
 └── ส่งไป DNS Server:
        127.0.0.1
             │
             ▼
       Local DNS Server
             │
             ▼
       PTR Record
             │
             ▼
    ipa-replica.lab.lan.
```

# ✅ ตัวอย่างผลลัพธ์

ถ้า DNS Server มี PTR Record สำหรับ `192.168.1.51` ผลลัพธ์อาจเป็น:

```text
ipa-replica.lab.lan.
```

ความหมายคือ:

```text
192.168.1.51
      ↓
ipa-replica.lab.lan.
```

แสดงว่า Reverse DNS สามารถค้นหา Hostname จาก IP Address ได้สำเร็จ

# 🧪 เปรียบเทียบ Forward และ Reverse DNS

### Forward DNS

ค้นจากชื่อ → IP

```text
dig +short ipa-replica.lab.lan @127.0.0.1
```

ผลลัพธ์:

```text
192.168.1.51
```

### Reverse DNS

ค้นจาก IP → ชื่อ

```text
dig +short -x 192.168.1.51 @127.0.0.1
```

ผลลัพธ์:

```text
ipa-replica.lab.lan.
```

ดังนั้นสองคำสั่งนี้ใช้ตรวจสอบ DNS คนละทิศทางกัน

```text
Forward DNS
Name ──────────────→ IP
ipa-replica.lab.lan → 192.168.1.51

Reverse DNS
IP ─────────────────→ Name
192.168.1.51        → ipa-replica.lab.lan.
```

# 🎯 สรุปสั้น ๆ

```text
dig
= เครื่องมือ Query DNS

+short
= แสดงเฉพาะคำตอบแบบสั้น

-x 192.168.1.51
= Reverse DNS Lookup ของ IP นี้

@127.0.0.1
= ให้ Query DNS Server ที่ localhost

ดังนั้น:

dig +short -x 192.168.1.51 @127.0.0.1

หมายถึง:

"ถาม DNS Server ที่อยู่บนเครื่องนี้ว่า
IP 192.168.1.51 มีชื่อ Hostname อะไร?"
```

หากเครื่องนี้เป็น **FreeIPA Server ที่ทำ DNS ด้วย BIND** คำสั่งนี้จึงเป็นวิธีที่ดีในการตรวจสอบว่า **PTR Record ที่สร้างไว้สามารถถูก Resolve จาก DNS Server ภายในเครื่องได้จริงหรือไม่**
