# 🔐 Linux Security Hardening: ป้องกัน Hard Link และ Symbolic Link Attack

คำสั่งชุดนี้มีประโยชน์ด้าน **Linux Security Hardening** โดยเฉพาะการป้องกันการโจมตีผ่าน **Hard Link และ Symbolic Link**

## 🔐 สิ่งที่คำสั่งนี้ทำ

```bash
sudo tee /etc/sysctl.d/60-fs_sysctl.conf > /dev/null <<EOF

fs.protected_hardlinks = 1

fs.protected_symlinks = 1

EOF

sudo sysctl --system
```

คำสั่งนี้กำหนด **Kernel Parameters** จำนวน 2 ตัว:

- `fs.protected_hardlinks = 1` → ป้องกัน **Hard Link Attack**
- `fs.protected_symlinks = 1` → ป้องกัน **Symbolic Link Attack**

---

## 🛡️ ทำไมจึงสำคัญ?

การโจมตีแบบ **Link Attack** มักอาศัยกรณีที่ผู้ใช้ทั่วไปสามารถสร้าง Link ไปยังไฟล์ที่มีสิทธิ์สูง เช่น ไฟล์ของ `root`

ตัวอย่างสถานการณ์:

```text
Attacker/User
     │
     ├── สร้าง Hard Link / Symbolic Link
     │
     ▼
ไฟล์ของ root หรือไฟล์สำคัญ
     │
     ▼
พยายามอ่าน/แก้ไข/เขียนไฟล์
     │
     ▼
Privilege Escalation
```

การตั้งค่าเหล่านี้ทำให้ Kernel **เพิ่มข้อจำกัดในการสร้างหรือ Follow Link ที่มีความเสี่ยง** ช่วยลดโอกาสเกิด:

- Local Privilege Escalation
- Hard Link Attack
- Symbolic Link Attack
- Symlink Race Condition
- Link-based attacks

---

# 📌 `fs.protected_hardlinks = 1`

ช่วยป้องกันการสร้าง **Hard Link** ไปยังไฟล์ที่ผู้โจมตีไม่มีสิทธิ์เหมาะสมในการเขียน

```bash
fs.protected_hardlinks = 1
```

เมื่อกำหนดเป็น `1` Kernel จะเพิ่มข้อจำกัดในการสร้าง Hard Link ไปยังไฟล์ที่ผู้สร้าง Link ไม่ควรสามารถ Link ได้

### 🛡️ ประโยชน์

ช่วยลดความเสี่ยงจาก:

**Hard Link Attack**

โดยเฉพาะกรณีที่ผู้ใช้ที่มีสิทธิ์ต่ำพยายามใช้ Hard Link เพื่อเข้าถึงหรือกระทบกับไฟล์ที่มีเจ้าของหรือสิทธิ์สูงกว่า

---

# 📌 `fs.protected_symlinks = 1`

ช่วยเพิ่มการป้องกันการ **Follow Symbolic Link** ใน Directory ที่มีความเสี่ยง เช่น `/tmp`

```bash
fs.protected_symlinks = 1
```

มีประโยชน์ในการลดความเสี่ยงจาก:

- Symlink Attack
- Symlink Race Condition
- Time-of-Check to Time-of-Use (TOCTOU) ที่เกี่ยวข้องกับ Symlink

ตัวอย่าง Directory ที่ควรระวังคือ:

```text
/tmp
/var/tmp
```

ซึ่งเป็น Directory ที่ผู้ใช้หลายคนสามารถสร้างไฟล์ได้

---

# 🔄 `sudo sysctl --system` คืออะไร?

หลังจากสร้างไฟล์:

```text
/etc/sysctl.d/60-fs_sysctl.conf
```

ด้วยคำสั่ง:

```bash
sudo sysctl --system
```

ระบบจะทำการโหลดค่า **sysctl configuration** จากไฟล์ Configuration ต่าง ๆ ที่เกี่ยวข้องกลับเข้า Kernel

ดังนั้นค่าที่เราเพิ่งกำหนดจะถูกนำมาใช้งานทันที โดย **ไม่จำเป็นต้อง Reboot**

---

# 🔍 ตรวจสอบค่าที่กำหนด

สามารถตรวจสอบได้ด้วย:

```bash
sysctl fs.protected_hardlinks
sysctl fs.protected_symlinks
```

ผลลัพธ์ที่ต้องการคือ:

```text
fs.protected_hardlinks = 1
fs.protected_symlinks = 1
```

หรือสามารถใช้:

```bash
sysctl -a | grep -E 'fs.protected_(hardlinks|symlinks)'
```

ผลลัพธ์:

```text
fs.protected_hardlinks = 1
fs.protected_symlinks = 1
```

---

# 📋 สรุป

| Kernel Parameter | หน้าที่ | Security Benefit |
|---|---|---|
| `fs.protected_hardlinks = 1` | จำกัดการสร้าง Hard Link ที่มีความเสี่ยง | ลดความเสี่ยง Hard Link Attack |
| `fs.protected_symlinks = 1` | จำกัดการ Follow Symlink ใน Directory ที่มีความเสี่ยง | ลดความเสี่ยง Symlink Attack |

## 🎯 Security Hardening

การตั้งค่า:

```bash
fs.protected_hardlinks = 1
fs.protected_symlinks = 1
```

ถือเป็น **Linux Kernel Security Hardening** ที่ช่วยเพิ่มการป้องกัน **Hard Link / Symbolic Link attacks**

และช่วยลดความเสี่ยงของ:

```text
Hard Link Attack
        │
        ▼
Symbolic Link Attack
        │
        ▼
File Manipulation
        │
        ▼
Privilege Escalation
```

ดังนั้นการตั้งค่า 2 ตัวนี้จึงเป็นหนึ่งใน **Defense-in-Depth Security Controls** ที่ควรพิจารณาในการ Hardening Linux Server
