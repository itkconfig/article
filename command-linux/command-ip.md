การใช้คำสั่ง `ip -4 address CONFFLAG-LIST` หรือ `ip -4 address CONFFLAG` แล้วเจอ Error เป็นเรื่องปกติครับ

ที่เป็นเช่นนั้นเพราะว่า `CONFFLAG-LIST` และ `CONFFLAG` ไม่ใช่ **"Command" (คำสั่ง)** ที่พิมพ์ลงไปได้ตรงๆ แต่เป็นเพียง **ตัวแปรโครงสร้าง (Placeholder)** ที่อธิบายว่าตรงตำแหน่งนั้นๆ คุณสามารถใส่อะไรลงไปได้บ้าง

ลองสังเกตวิธีที่หน้า Help เขียนอธิบาย:

```text
CONFFLAG-LIST := [ CONFFLAG-LIST ] CONFFLAG
CONFFLAG      := [ home | nodad | mngtmpaddr | noprefixroute | autojoin ]
```

**ความหมายคือ:**
เมื่อคุณจะพิมพ์ค่าแทนที่ตำแหน่ง `CONFFLAG` ในคำสั่งหลัก คุณต้องเลือกใช้คำที่อยู่ในวงเล็บเหลี่ยม `[ ... ]` คำใดคำหนึ่งครับ เช่น `home`, `nodad`, หรือ `noprefixroute`

**ตัวอย่างการนำไปใช้จริง:**
หากคุณต้องการเพิ่ม IP และใส่ Flag แบบ `noprefixroute` เข้าไปด้วย (ซึ่ง `noprefixroute` ก็คือหนึ่งใน `CONFFLAG`) คุณจะต้องพิมพ์คำสั่งประมาณนี้ครับ:

```bash
# โครงสร้าง: ip address add IFADDR dev IFNAME [ CONFFLAG-LIST ]
ip address add 192.168.1.10/24 dev ens160 noprefixroute
```

> **💡 ข้อสังเกต:**
> จำไว้เสมอว่าคำที่เป็น **ตัวพิมพ์ใหญ่ทั้งหมด** ในหน้า Help (เช่น `IFADDR`, `IFNAME`, `SCOPE-ID`, `FLAG`, `TYPE`) เป็นแค่ **ชื่อตัวแทน (Placeholder)** เพื่อให้คุณไปดูบรรทัดด้านล่างว่าต้องพิมพ์คำจริงๆ ว่าอะไรครับ
