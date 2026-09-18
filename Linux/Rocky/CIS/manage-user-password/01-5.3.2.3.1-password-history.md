# 🔐 อธิบายคำสั่ง `sed` แบบละเอียด

คำสั่ง:

sudo sed -ri 's/^\s*#?\s*remember\s*=.*/remember = 24/' /etc/security/pwhistory.conf

คำสั่งนี้ใช้สำหรับ **แก้ไขไฟล์ `/etc/security/pwhistory.conf` โดยตรง**
เพื่อกำหนดค่า:

    remember = 24

ซึ่งโดยทั่วไปใช้กับ PAM Password History เพื่อกำหนดจำนวน Password เก่าที่ระบบจะจดจำไว้


---

# 1. แยกโครงสร้างคำสั่ง

    sudo sed -ri 's/^\s*#?\s*remember\s*=.*/remember = 24/' /etc/security/pwhistory.conf

สามารถแบ่งได้เป็น:

    sudo
    │
    └── sed
        │
        ├── -r
        ├── -i
        │
        ├── 's/^\s*#?\s*remember\s*=.*/remember = 24/'
        │
        └── /etc/security/pwhistory.conf


---

# 2. `sudo`

    sudo

ใช้รันคำสั่งด้วยสิทธิ์ของ `root`

ไฟล์:

    /etc/security/pwhistory.conf

เป็นไฟล์ configuration ของระบบ ซึ่งผู้ใช้ทั่วไปมักไม่มีสิทธิ์เขียน

ดังนั้นจึงใช้:

    sudo

เพื่อให้ `sed` สามารถแก้ไขไฟล์ได้


---

# 3. `sed` คืออะไร?

`sed` ย่อมาจาก:

    Stream Editor

เป็นเครื่องมือใน Linux สำหรับ:

- ค้นหาข้อความ
- แทนที่ข้อความ
- ลบข้อความ
- เพิ่ม/แทรกข้อความ
- แก้ไขข้อความตาม Pattern
- ประมวลผลไฟล์ข้อความแบบอัตโนมัติ

ตัวอย่างพื้นฐาน:

    sed 's/old/new/' file

หมายถึง:

    ค้นหา old
    ↓
    เปลี่ยนเป็น new


---

# 4. ตัวเลือก `-r`

    -r

หมายถึง:

    Extended Regular Expressions (ERE)

ทำให้ `sed` สามารถใช้ Regular Expression แบบ Extended ได้ง่ายขึ้น

ในคำสั่งนี้ Pattern ที่ใช้ เช่น:

    \s*
    ?
    .*

เป็นส่วนหนึ่งของ Regular Expression


> หมายเหตุ:
> บน GNU sed สามารถใช้ `-E` แทน `-r` ได้ และ `-E` เป็นรูปแบบที่นิยมใช้ในคำสั่งใหม่ ๆ มากกว่า


ตัวอย่าง:

    sed -E 's/.../.../' file

เทียบเท่ากับ:

    sed -r 's/.../.../' file


---

# 5. ตัวเลือก `-i`

    -i

ย่อมาจาก:

    --in-place

หมายถึง:

    แก้ไขไฟล์จริงโดยตรง

ถ้าไม่มี `-i`:

    sed 's/old/new/' file

`sed` จะ:

    อ่านไฟล์
    ↓
    แก้ไขข้อมูลใน Output
    ↓
    แสดงผลทางหน้าจอ

แต่ไฟล์ต้นฉบับจะไม่ถูกเปลี่ยน


เมื่อใช้:

    sed -i 's/old/new/' file

จะเป็น:

    อ่านไฟล์
    ↓
    แก้ไข
    ↓
    เขียนผลกลับลงไฟล์


ดังนั้นคำสั่งของเราจะเปลี่ยน:

    /etc/security/pwhistory.conf

จริง ๆ


---

# 6. ส่วนที่สำคัญที่สุด

ส่วนนี้คือคำสั่ง `sed`:

    's/^\s*#?\s*remember\s*=.*/remember = 24/'


โครงสร้าง:

    s / SEARCH_PATTERN / REPLACEMENT /


โดย:

    s

หมายถึง:

    substitute

หรือ:

    "ค้นหาแล้วแทนที่"


ดังนั้น:

    s/Pattern/Replacement/

หมายถึง:

    ค้นหา Pattern
    ↓
    แทนที่ด้วย Replacement


---

# 7. วิเคราะห์ Pattern ทีละส่วน

Pattern คือ:

    ^\s*#?\s*remember\s*=.*

แยกได้ดังนี้:

    ^
    \s*
    #?
    \s*
    remember
    \s*
    =
    .*


มาดูทีละส่วน


---

# 8. `^`

    ^

หมายถึง:

    จุดเริ่มต้นของบรรทัด

ตัวอย่าง:

    remember = 5

ตรงกับ:

    ^remember

แต่:

    abc remember = 5

จะไม่ตรงกับ:

    ^remember


เพราะ `remember` ไม่ได้อยู่ต้นบรรทัด


---

# 9. `\s*`

    \s*

หมายถึง:

    whitespace 0 ตัว หรือมากกว่า


Whitespace ได้แก่:

- Space
- Tab


เครื่องหมาย:

    \s

หมายถึง whitespace


ส่วน:

    *

หมายถึง:

    0 หรือมากกว่า


ดังนั้น:

    \s*

หมายถึง:

    มีช่องว่างหรือไม่มีก็ได้


ตัวอย่างที่สามารถ match ได้:

    remember = 5

    remember    = 5

        remember = 5

    remember=5


---

# 10. `#?`

    #?

หมายถึง:

    เครื่องหมาย # มี 0 หรือ 1 ตัว


เครื่องหมาย:

    ?

หมายถึง:

    มี 0 หรือ 1 ครั้ง


ดังนั้น:

    #?

สามารถ match ได้ทั้ง:

    #
    
หรือ:

    ไม่มี #


นี่สำคัญมาก เพราะทำให้คำสั่งรองรับทั้ง Configuration ที่ถูก Comment และไม่ถูก Comment


ตัวอย่าง:

    # remember = 5

และ:

    remember = 5


ทั้งสองแบบสามารถ match ได้


---

# 11. `\s*` หลัง `#?`

ส่วนนี้:

    \s*

รองรับช่องว่างหลัง `#`

เช่น:

    # remember = 5

หรือ:

    #    remember = 5


ทั้งสองแบบสามารถ match ได้


---

# 12. `remember`

    remember

คือคำ Keyword ที่เราต้องการค้นหา


ตัวอย่าง:

    remember = 5

    remember = 10

    remember = 24


ทั้งหมดมีคำว่า:

    remember


---

# 13. `\s*` ก่อน `=`

ส่วนนี้:

    \s*

รองรับช่องว่างก่อนเครื่องหมาย `=`


จึงสามารถ match:

    remember=5

    remember =5

    remember= 5

    remember = 5

    remember     =     5


---

# 14. `=`

    =

หมายถึงเครื่องหมายเท่ากับแบบตรงตัว


ดังนั้น Pattern ต้องมี:

    remember

ตามด้วย optional whitespace

แล้วตามด้วย:

    =


---

# 15. `.*`

    .*

เป็นส่วนที่สำคัญมาก


`.` หมายถึง:

    ตัวอักษรใด ๆ


`*` หมายถึง:

    0 ตัวหรือมากกว่า


ดังนั้น:

    .*

หมายถึง:

    ข้อความอะไรก็ได้หลังจากนั้น


ตัวอย่าง:

    remember = 5

    remember = 10

    remember = 24

    remember = 100


ทั้งหมด match:

    remember\s*=.*


เพราะหลัง `=` จะเป็นอะไรก็ได้


---

# 16. ดังนั้น Pattern ทั้งหมดหมายความว่าอะไร?

    ^\s*#?\s*remember\s*=.*

แปลเป็นภาษาคนได้ว่า:

    เริ่มจากต้นบรรทัด
    ↓
    อนุญาตให้มีช่องว่างก่อน
    ↓
    อาจมี # หรือไม่มีก็ได้
    ↓
    อนุญาตให้มีช่องว่าง
    ↓
    ต้องพบคำว่า remember
    ↓
    อนุญาตให้มีช่องว่าง
    ↓
    ต้องพบ =
    ↓
    หลังจากนั้นจะเป็นข้อความอะไรก็ได้


จึงสามารถจับบรรทัดลักษณะต่าง ๆ เช่น:

    remember = 5

    remember=5

        remember = 5

    # remember = 5

    #remember = 5

    #    remember    =    5


---

# 17. ส่วน Replacement

หลัง `/` ตัวที่สองคือ:

    remember = 24


หมายถึง:

    เมื่อเจอบรรทัดที่ Match
    ↓
    แทนที่ทั้งบรรทัดด้วย
    ↓
    remember = 24


ตัวอย่าง:

    remember = 5

จะกลายเป็น:

    remember = 24


หรือ:

    # remember = 5

จะกลายเป็น:

    remember = 24


หรือ:

        remember = 10

จะกลายเป็น:

    remember = 24


---

# 18. ทำไมต้องใช้ `.*`?

สมมติไฟล์มี:

    remember = 5


Pattern:

    ^\s*#?\s*remember\s*=.*

จะ Match ตั้งแต่ต้นจนจบบรรทัด:

    remember = 5
    ^^^^^^^^^^^^^


จากนั้น Replacement:

    remember = 24


จึงแทนที่ทั้งบรรทัด


ผลลัพธ์:

    remember = 24


---

# 19. ตัวอย่างก่อนและหลัง

## ก่อน

    # remember = 5

## หลัง

    remember = 24


---

## ก่อน

    remember = 5

## หลัง

    remember = 24


---

## ก่อน

        remember=10

## หลัง

    remember = 24


---

## ก่อน

    remember    =    10

## หลัง

    remember = 24


---

# 20. ทำไมคำสั่งนี้รองรับทั้ง Comment และ Active Configuration?

เพราะมี:

    #?


เครื่องหมาย `#` เป็น Optional


ดังนั้น:

    # remember = 5

และ:

    remember = 5

จะถูกมองว่าเป็น Configuration เดียวกันในมุมของ Pattern


แล้ว `sed` จะเปลี่ยนทั้งสองแบบเป็น:

    remember = 24


---

# 21. ถ้าไม่มี `-i` จะเกิดอะไรขึ้น?

ลอง:

    sudo sed -r 's/^\s*#?\s*remember\s*=.*/remember = 24/' /etc/security/pwhistory.conf


ผลลัพธ์จะถูกส่งออกทาง Terminal


แต่ไฟล์:

    /etc/security/pwhistory.conf

จะยังไม่ถูกแก้ไข


ดังนั้น:

    -r

คือ Regex mode

แต่:

    -i

คือสิ่งที่ทำให้ไฟล์ถูกแก้จริง


---

# 22. คำสั่งนี้ทำงานอย่างไรเป็นลำดับ?

สามารถมองเป็น Flow ได้:

    /etc/security/pwhistory.conf
              │
              ▼
        sed อ่านทีละบรรทัด
              │
              ▼
    ตรวจสอบ Pattern
              │
              ▼
    ^\s*#?\s*remember\s*=.*
              │
       ┌──────┴──────┐
       │             │
     Match        ไม่ Match
       │             │
       ▼             ▼
    Replace         คงเดิม
       │
       ▼
    remember = 24
       │
       ▼
    -i เขียนกลับไฟล์
              │
              ▼
    /etc/security/pwhistory.conf


---

# 23. ตัวอย่างสถานการณ์จริง

สมมติไฟล์มี:

    # Password history configuration

    # remember = 5

    retry = 3

    enforce_for_root


เมื่อรัน:

    sudo sed -ri 's/^\s*#?\s*remember\s*=.*/remember = 24/' /etc/security/pwhistory.conf


จะได้:

    # Password history configuration

    remember = 24

    retry = 3

    enforce_for_root


เฉพาะบรรทัด `remember` ถูกแก้


---

# 24. จุดสำคัญ: ถ้าไม่มี `remember` อยู่เลย

นี่เป็นข้อจำกัดสำคัญของคำสั่งนี้


ถ้าไฟล์ไม่มี:

    remember = ...

หรือ:

    # remember = ...


คำสั่ง `sed` นี้จะ:

    ไม่เพิ่มบรรทัดใหม่


ตัวอย่าง:

    # Password history configuration
    retry = 3

หลังรันคำสั่ง:

    # Password history configuration
    retry = 3


จะไม่มี:

    remember = 24


เพราะ `sed` คำสั่งนี้เป็นเพียง:

    "ค้นหาแล้วแทนที่"


ไม่ใช่:

    "ถ้าไม่มีให้เพิ่ม"


---

# 25. ถ้ามี `remember` หลายบรรทัด

ตัวอย่าง:

    # remember = 5
    remember = 10


คำสั่งนี้จะ Match ทั้งสองบรรทัด


ผลลัพธ์:

    remember = 24
    remember = 24


ดังนั้นไฟล์อาจมีค่า `remember` ซ้ำกัน


สำหรับ Configuration File เรื่องนี้ควรระวัง เพราะผลลัพธ์ที่โปรแกรมใช้จริงอาจขึ้นกับ parser ของ configuration นั้น


---

# 26. วิธีตรวจสอบก่อนแก้

แนะนำให้ตรวจสอบก่อน:

    sudo grep -nE '^\s*#?\s*remember\s*=' /etc/security/pwhistory.conf


ตัวอย่างผลลัพธ์:

    12:# remember = 5


หรือ:

    12:remember = 5


หมายเลขด้านหน้า:

    12:

คือหมายเลขบรรทัด


---

# 27. ดู Configuration หลังแก้

หลังจากรัน:

    sudo sed -ri 's/^\s*#?\s*remember\s*=.*/remember = 24/' /etc/security/pwhistory.conf


ตรวจสอบ:

    grep -nE '^\s*remember\s*=' /etc/security/pwhistory.conf


ควรได้:

    12:remember = 24


---

# 28. แนะนำให้ Backup ก่อนใช้ `sed -i`

เพราะ:

    sed -i

แก้ไฟล์จริง


สามารถสร้าง Backup ได้โดยใช้:

    sudo sed -ri.bak 's/^\s*#?\s*remember\s*=.*/remember = 24/' /etc/security/pwhistory.conf


จะได้ไฟล์:

    /etc/security/pwhistory.conf

และ Backup:

    /etc/security/pwhistory.conf.bak


ถ้าต้องการย้อนกลับ:

    sudo cp -a /etc/security/pwhistory.conf.bak /etc/security/pwhistory.conf


---

# 29. ความแตกต่างระหว่าง `-i` และ `-i.bak`

## แบบไม่มี Backup

    sudo sed -ri 's/^\s*#?\s*remember\s*=.*/remember = 24/' /etc/security/pwhistory.conf


แก้ไฟล์โดยตรง


## แบบมี Backup

    sudo sed -ri.bak 's/^\s*#?\s*remember\s*=.*/remember = 24/' /etc/security/pwhistory.conf


แก้ไฟล์ และสร้าง:

    /etc/security/pwhistory.conf.bak


สำหรับงาน Security Hardening การมี Backup ก่อนแก้ Configuration มักช่วยให้ Rollback ได้ง่ายกว่า


---

# 30. สรุปแต่ละส่วน

คำสั่งเต็ม:

    sudo sed -ri 's/^\s*#?\s*remember\s*=.*/remember = 24/' /etc/security/pwhistory.conf


ความหมาย:

    sudo
    → ใช้สิทธิ์ root

    sed
    → Stream Editor

    -r
    → ใช้ Extended Regular Expression

    -i
    → แก้ไฟล์จริงโดยตรง

    s
    → substitute / แทนที่

    ^
    → จุดเริ่มต้นบรรทัด

    \s*
    → whitespace 0 ตัวขึ้นไป

    #?
    → # มีหรือไม่มีก็ได้

    remember
    → Keyword ที่ต้องการค้นหา

    \s*
    → whitespace ก่อน =

    =
    → เครื่องหมายเท่ากับ

    .*
    → ข้อความที่เหลือทั้งบรรทัด

    remember = 24
    → ข้อความใหม่ที่ใช้แทน

    /etc/security/pwhistory.conf
    → ไฟล์เป้าหมาย


---

# 🧠 จำง่าย ๆ

คำสั่งนี้สามารถอ่านเป็นประโยคได้ว่า:

    "ในไฟล์ pwhistory.conf
     ให้ค้นหาบรรทัดที่มี remember =
     ไม่ว่าจะมี # หรือมีช่องว่างนำหน้าหรือไม่
     แล้วเปลี่ยนทั้งบรรทัดเป็น
     remember = 24
     และเขียนการเปลี่ยนแปลงลงไฟล์จริง"


หรือจำ Pattern:

    s / ของเก่า / ของใหม่ /


ในคำสั่งนี้:

    ของเก่า:
    ^\s*#?\s*remember\s*=.*

    ของใหม่:
    remember = 24


ดังนั้นแก่นของคำสั่งคือ:

    sed
    ↓
    ค้นหา "remember = ..."
    ↓
    แทนที่ด้วย "remember = 24"
    ↓
    -i
    ↓
    เขียนกลับเข้าไฟล์
