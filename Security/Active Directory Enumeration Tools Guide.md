# Active Directory Enumeration Tools Guide (คู่มือเครื่องมือการสำรวจระบบ Active Directory)

ภาพรวมบทความนี้สรุปโครงสร้างและหมวดหมู่ของเครื่องมือสแกนและสำรวจความปลอดภัยบน Active Directory (Active Directory Enumeration Tools) จากผังรวบรวมเครื่องมือของ Hacking Articles [1]

---

## 1. บทนำ (Introduction)

การสำรวจข้อมูล (Enumeration) ในระบบ Active Directory (AD) เป็นขั้นตอนสำคัญอย่างยิ่งในการประเมินความปลอดภัยและการทดสอบเจาะระบบ [1] ผังการทำงาน Active Directory Enumeration Tools รวบรวมและจัดกลุ่มเครื่องมือต่าง ๆ ตามเป้าหมายและลักษณะการใช้งานออกเป็นหมวดหมู่อย่างเป็นระบบ [1]

---

## 2. หมวดหมู่เครื่องมือการสำรวจระบบ Active Directory (Tool Categories)

### 2.1 Active Directory Recon & Graph-Based Enumeration
กลุ่มเครื่องมือที่ใช้สำหรับการค้นหา วิเคราะห์โครงสร้างภาพรวม และสร้างกราฟความสัมพันธ์ของ Active Directory [1]:
- **Active Directory Recon**: BloodHound CE, SharpHound, BloodHound.py, PingCastle, Purple Knight, ADRecon, ADExplorer, ADExplorersnapshot.py [1]
- **Graph-Based Enumeration**: BloodHound CE, AzureHound, SharpHound, BloodHound.py [1]

### 2.2 Network, Domain & Protocol-Specific Enumeration
กลุ่มเครื่องมือสำหรับสำรวจเครือข่าย โดเมน และโปรโตคอลหลักในระบบ [1]:
- **Network & Domain Enumeration**: NetExec (NXC), enum4linux-ng, rpcclient, smbclient, smbmap, ldapsearch, ldapdomaindump, windapsearch, adidnsdump [1]
- **LDAP Enumeration**: ldapsearch, windapsearch, ldapdomaindump, NetExec LDAP [1]
- **SMB Enumeration**: smbmap, smbclient, NetExec SMB, rpcclient [1]
- **DNS Enumeration**: adidnsdump, dnsenum, dig, nslookup [1]

### 2.3 PowerShell & Local Enumeration
กลุ่มเครื่องมือที่รันคำสั่งสแกนผ่าน PowerShell หรือการสำรวจข้อมูลภายในเครื่องท้องถิ่น (Local Host) [1]:
- **PowerShell Enumeration**: PowerView, SharpView, PowerSploit, Nishang [1]
- **Local Enumeration**: Seatbelt, winPEAS, SharpUp, Watson, SharpEDRChecker [1]

### 2.4 Credential, Kerberos & Password Policy Enumeration
กลุ่มเครื่องมือที่เน้นการค้นหา บัตรผ่านสิทธิ์ (Credentials), ตรวจสอบโปรโตคอล Kerberos และนโยบายรหัสผ่าน [1]:
- **Credential Enumeration**: NetExec, Kerbrute, GetNPUsers.py, GetUserSPNs.py, Rubeus, Certipy, PKINITtools [1]
- **Kerberos Enumeration**: Kerbrute, Rubeus, Kekeo, Impacket, PKINITtools [1]
- **Password Policy Enumeration**: enum4linux-ng, NetExec, rpcclient, ldapsearch [1]

### 2.5 Impacket Collection & Specialized AD Services
กลุ่มเครื่องมือสคริปต์ Impacket และการตรวจเช็คบริการเฉพาะทางใน Active Directory [1]:
- **Impacket Collection**: lookupsid.py, GetADUsers.py, GetNPUsers.py, GetUserSPNs.py, findDelegation.py, secretsdump.py, wmiexec.py, psexec.py, smbexec.py, atexec.py, rpcdump.py, samrdump.py [1]
- **Active Directory Certificate Services (ADCS)**: Certipy, Certify, ForgeCert, PSPKIAudit [1]
- **ACL Enumeration**: BloodHound, PowerView, dacledit.py, BloodyAD [1]
- **Trust Enumeration**: BloodHound, nltest, PowerView, findDelegation.py [1]
- **Group Policy Enumeration**: SharpGPOAbuse, Grouper2, PowerView [1]

### 2.6 Cloud & Hybrid Active Directory
กลุ่มเครื่องมือสำหรับการตรวจสอบ Active Directory บนระบบคลาวด์และสภาพแวดล้อมแบบไฮบริด [1]:
- **Cloud & Hybrid AD**: AzureHound, ROADtools, AADInternals, GraphRunner [1]

### 2.7 Comprehensive Frameworks
ชุดเฟรมเวิร์กและเครื่องมือหลักที่ครอบคลุมการทำงานหลากหลายมิติ [1]:
- **Comprehensive Frameworks**: NetExec, BloodHound, Impacket, PowerView, Certipy, BloodyAD, CrackHound, PingCastle, ADRecon [1]

---

## 3. สรุป (Summary)

แผนผังเครื่องมือ Active Directory Enumeration ให้ภาพรวมครอบคลุมเครื่องมือตั้งแต่การสแกนระดับ Network/Protocol (เช่น SMB, LDAP, DNS), การวิเคราะห์สิทธิ์และนโยบาย (ACL, GPO, Password Policy), การตรวจสอบโปรโตคอลเฉพาะ (Kerberos, ADCS) ไปจนถึงสภาพแวดล้อม Cloud & Hybrid AD [1]

---


