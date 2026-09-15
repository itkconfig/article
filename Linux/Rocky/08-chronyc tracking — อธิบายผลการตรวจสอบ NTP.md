# `chronyc tracking` — อธิบายผลการตรวจสอบ NTP

คำสั่ง `chronyc tracking` ใช้ตรวจสอบสถานะการ Synchronize เวลาของเครื่อง Linux กับ NTP Server โดยจะแสดงข้อมูลเกี่ยวกับแหล่งอ้างอิงเวลา ความคลาดเคลื่อน ความแม่นยำของ Clock และสถานะการ Synchronize

## ตัวอย่าง Log

    [itk@lab-linux-node-01 ~]$ chronyc tracking
    Reference ID    : CB9F4621 (ntp1.bknix.co.th)
    Stratum         : 2
    Ref time (UTC)  : Tue Sep 15 12:34:11 2026
    System time     : 0.000266168 seconds slow of NTP time
    Last offset     : -0.000151310 seconds
    RMS offset      : 0.000139296 seconds
    Frequency       : 19.378 ppm fast
    Residual freq   : -0.011 ppm
    Skew            : 0.271 ppm
    Root delay      : 0.006798317 seconds
    Root dispersion : 0.001624191 seconds
    Update interval : 256.7 seconds
    Leap status     : Normal

---

## ความหมายของแต่ละค่า

### 1. `Reference ID`

    Reference ID : CB9F4621 (ntp1.bknix.co.th)

แสดง NTP Server ที่เครื่องกำลังใช้เป็นแหล่งอ้างอิงเวลา

- `CB9F4621` คือ Reference ID ของ NTP Server
- `ntp1.bknix.co.th` คือชื่อ NTP Server
- เครื่อง `lab-linux-node-01` กำลัง Synchronize เวลากับ Server นี้

---

### 2. `Stratum`

    Stratum : 2

แสดงระดับชั้นของแหล่งอ้างอิงเวลาในระบบ NTP

ตัวอย่างลำดับชั้น:

    Stratum 0
        │
        ├── Atomic Clock
        ├── GPS
        └── Reference Clock
                │
                ▼
            Stratum 1
                │
                └── NTP Server
                        │
                        ▼
                    Stratum 2
                        │
                        └── NTP Server
                                │
                                ▼
                            Stratum 3
                                │
                                └── Linux Server / Client

โดยทั่วไป Stratum ยิ่งต่ำ หมายถึงอยู่ใกล้แหล่งอ้างอิงเวลามากกว่า

เครื่องนี้กำลังอ้างอิง NTP Server ที่มีค่า:

    Stratum = 2

ถือว่าเป็นแหล่งอ้างอิงเวลาที่ดีสำหรับระบบทั่วไป

---

### 3. `Ref time (UTC)`

    Ref time (UTC) : Tue Sep 15 12:34:11 2026

คือเวลาที่ NTP Server ใช้เป็น Reference Time ล่าสุด

ค่าที่แสดงเป็น **UTC**

ประเทศไทยใช้เวลา UTC+7 ดังนั้น:

    12:34:11 UTC
          +
        07:00
          =
    19:34:11 Thailand

---

### 4. `System time`

    System time : 0.000266168 seconds slow of NTP time

แสดงความแตกต่างระหว่างเวลาของเครื่องกับเวลาจาก NTP Server

ค่าคือ:

    0.000266168 seconds
    ≈ 0.266 milliseconds
    ≈ 266 microseconds

หมายความว่าเวลาของเครื่อง **ช้ากว่า NTP Time ประมาณ 0.266 ms**

ถือว่าคลาดเคลื่อนน้อยมาก

---

### 5. `Last offset`

    Last offset : -0.000151310 seconds

แสดงค่า Offset จากการวัดครั้งล่าสุด

มีค่าประมาณ:

    -0.000151310 seconds
    ≈ -0.151 milliseconds
    ≈ -151 microseconds

หมายความว่าในการวัดครั้งล่าสุด เวลาของเครื่องมีความแตกต่างจาก NTP Server ประมาณ **0.151 ms**

---

### 6. `RMS offset`

    RMS offset : 0.000139296 seconds

RMS ย่อมาจาก **Root Mean Square**

ใช้แสดงค่าความคลาดเคลื่อนของเวลาโดยรวมจากการวัดที่ผ่านมา

ค่าประมาณ:

    0.000139296 seconds
    ≈ 0.139 milliseconds

ค่าต่ำแสดงว่าเวลาของระบบมีความเสถียรและ Synchronize ได้ดี

---

### 7. `Frequency`

    Frequency : 19.378 ppm fast

แสดงความเร็วของ Hardware Clock เมื่อเปรียบเทียบกับ Reference Clock

`ppm` ย่อมาจาก:

    parts per million

ค่า:

    19.378 ppm fast

หมายความว่า Hardware Clock ของเครื่องมีแนวโน้มเดินเร็วกว่า Reference Clock ประมาณ 19.378 ส่วนต่อล้าน

Chrony จะนำข้อมูลนี้ไปใช้ในการปรับแก้เวลาโดยอัตโนมัติ

---

### 8. `Residual freq`

    Residual freq : -0.011 ppm

แสดงค่าความคลาดเคลื่อนของ Frequency ที่เหลืออยู่หลังจาก chrony ทำการปรับแก้

ค่าของเครื่องนี้:

    -0.011 ppm

ค่าใกล้เคียง `0` มาก แสดงว่า Chrony สามารถประมาณและชดเชย Frequency ของ Clock ได้ดี

---

### 9. `Skew`

    Skew : 0.271 ppm

แสดงค่าความไม่แน่นอนของ Frequency ที่ chrony ประเมินไว้

โดยทั่วไปค่าที่ต่ำหมายถึงการประมาณ Frequency มีความแม่นยำมากขึ้น

เครื่องนี้มีค่า:

    0.271 ppm

ถือว่าดีมาก

---

### 10. `Root delay`

    Root delay : 0.006798317 seconds

แสดงค่าความล่าช้าที่เกี่ยวข้องกับการสื่อสารไปยัง NTP Reference Clock

ค่าประมาณ:

    0.006798317 seconds
    ≈ 6.8 milliseconds

แสดงว่าเส้นทาง Network ที่ใช้สำหรับ NTP มี Delay ประมาณ 6.8 ms

---

### 11. `Root dispersion`

    Root dispersion : 0.001624191 seconds

แสดงค่าประมาณของความคลาดเคลื่อนสะสมของเวลาใน NTP hierarchy

ค่าประมาณ:

    0.001624191 seconds
    ≈ 1.624 milliseconds

ค่าที่ต่ำแสดงว่า NTP Time มีความคลาดเคลื่อนสะสมต่ำ

---

### 12. `Update interval`

    Update interval : 256.7 seconds

แสดงช่วงเวลาที่ chrony ใช้ในการ Update/Synchronize กับ NTP Server

ค่าประมาณ:

    256.7 seconds
    ≈ 4.3 minutes

ค่า Interval สามารถเปลี่ยนแปลงได้ตามสถานะและความเสถียรของระบบเวลา

---

### 13. `Leap status`

    Leap status : Normal

แสดงสถานะเกี่ยวกับ Leap Second

ค่า:

    Normal

หมายความว่าขณะนี้ระบบเวลาอยู่ในสถานะปกติ และไม่มี Leap Second Event ที่ต้องดำเนินการ

---

# สรุปผลของเครื่อง `lab-linux-node-01`

จากผล `chronyc tracking` ชุดนี้ สามารถสรุปได้ว่า NTP ของเครื่องทำงานได้ดีมาก

    NTP Server       : ntp1.bknix.co.th
    Stratum          : 2
    Current offset   : ~0.266 ms
    Last offset      : ~0.151 ms
    RMS offset       : ~0.139 ms
    Network delay    : ~6.8 ms
    Frequency skew   : 0.271 ppm
    Leap status      : Normal

สถานะโดยรวม:

    🟢 NTP Synchronization : GOOD

---

# จำง่าย ๆ

| ค่า | ความหมาย |
|---|---|
| `Reference ID` | NTP Server ที่ใช้อ้างอิง |
| `Stratum` | ระดับชั้นของ NTP |
| `Ref time` | เวลาของ Reference Server |
| `System time` | เวลาของเครื่องต่างจาก NTP เท่าไร |
| `Last offset` | Offset ล่าสุด |
| `RMS offset` | ค่า Offset โดยรวม |
| `Frequency` | Clock ของเครื่องเร็วหรือช้า |
| `Residual freq` | Frequency error ที่เหลือ |
| `Skew` | ความไม่แน่นอนของ Frequency |
| `Root delay` | Network delay ของ NTP |
| `Root dispersion` | ความคลาดเคลื่อนสะสมของเวลา |
| `Update interval` | ช่วงเวลาการ Update |
| `Leap status` | สถานะ Leap Second |

---

# ทำไม NTP สำคัญกับระบบ Security?

การ Synchronize เวลาให้ตรงกันมีความสำคัญมากในระบบ Security และ SOC เพราะ Log จากหลายเครื่องต้องใช้ Timestamp เดียวกันในการวิเคราะห์เหตุการณ์

ตัวอย่าง:

    Linux Server
         │
         ├── SSH Log
         ├── Audit Log
         └── System Log
                │
                ▼
              Wazuh
                │
                ▼
             Graylog
                │
                ▼
         Security Analyst

ถ้าเวลาของแต่ละเครื่องไม่ตรงกัน อาจทำให้การเรียงลำดับเหตุการณ์ผิด เช่น:

    19:30:01  SSH Login
    19:29:58  sudo
    19:30:05  File Modified

ทั้งที่เหตุการณ์จริงอาจเกิดตามลำดับ:

    19:29:58  SSH Login
    19:30:01  sudo
    19:30:05  File Modified

ดังนั้น NTP ที่มีค่า Offset ต่ำและมีสถานะ `Leap status : Normal` จึงมีความสำคัญต่อ:

- Log Correlation
- Threat Hunting
- Incident Response
- Wazuh
- Graylog
- Linux Audit
- Security Monitoring

## สรุป

เครื่อง `lab-linux-node-01` มีการ Synchronize เวลากับ `ntp1.bknix.co.th` ซึ่งเป็น NTP Server ระดับ Stratum 2

ค่าความคลาดเคลื่อนของเวลาอยู่ในระดับประมาณ **0.1–0.3 milliseconds** และ `Leap status` เป็น `Normal`

ดังนั้นสถานะโดยรวมของ NTP:

    🟢 GOOD

เหมาะสำหรับใช้งานด้าน **Log Correlation, SOC Monitoring, Wazuh, Graylog และ Threat Hunting**
