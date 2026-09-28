1. เช็คไฟล์ Alerts ดิบ (มีเหตุการณ์เข้าเงื่อนไข Ruleset ไหม)
Wazuh จะเขียน Alert ที่วิเคราะห์แล้วลงในไฟล์นี้แบบ Real-time:

Bash
# มอนิเตอร์ดู Alerts ล่าสุดแบบเรียลไทม์
sudo tail -f /var/ossec/logs/alerts/alerts.log
หรือหากต้องการดูแบบ JSON:

Bash
sudo tail -f /var/ossec/logs/alerts/alerts.json
(หากมีการพิมพ์รหัสผ่านผิด, รัน sudo, หรือสั่ง restart agent บรรทัดใหม่จะวิ่งขึ้นมาทันที)

2. เช็คไฟล์ Archive (ตรวจดู Log ดิบทั้งหมด ทุกบรรทัดที่ส่งเข้ามา)
ตามค่าเริ่มต้น Wazuh จะบันทึกเฉพาะ Log ที่ ตรงกับกฎ (Alerts) เท่านั้น หากต้องการดู Log ทั้งหมดที่ Agent ส่งเข้ามา (แม้จะไม่ Trigger เป็น Alert):

เปิดไฟล์คอนฟิก:

Bash
sudo nano /var/ossec/etc/ossec.conf
ค้นหาบล็อก <global> แล้วเปลี่ยนค่า logall หรือ logall_json เป็น yes:

XML
<ossec_config>
  <global>
    <logall>yes</logall>
    <logall_json>yes</logall_json>
    ...
  </global>
สั่ง Restart Manager:

Bash
sudo systemctl restart wazuh-manager
ดู Log ดิบทั้งหมดที่วิ่งเข้าเครื่อง:

Bash
sudo tail -f /var/ossec/logs/archives/archives.log
3. เช็คสถานะการเชื่อมต่อของ Network Port
ตรวจสอบว่าพอร์ตสื่อสารระหว่าง Agent กับ Manager เปิดรับทราฟฟิกอยู่หรือไม่:

Bash
# พอร์ต 1514 (TCP/UDP) = รับ Event จาก Agent
# พอร์ต 1515 (TCP)     = รับ Registration/Enrollment
sudo ss -tulpn | grep -E '1514|1515'
ดักฟัง Packet สดๆ ว่ามีข้อมูลวิ่งมาจาก Agent จริงไหม (แทนค่า <AGENT_IP>):

Bash
sudo tcpdump -i any port 1514 -nn
4. ตรวจสอบสถานะของ Agent จากคำสั่ง CLI บน Manager
ตรวจดูว่าเครื่องปลายทางอยู่ในสถานะ Active หรือไม่:

Bash
sudo /var/ossec/bin/agent_control -l
หรือดูรายละเอียดและเวลาติดต่อล่าสุดของ Agent นั้นๆ:

Bash
sudo /var/ossec/bin/agent_control -i <AGENT_ID>
(สังเกตตรงบรรทัด Last keep alive: หากเวลาอัปเดตเป็นปัจจุบัน แสดงว่า Agent ติดต่อและส่งข้อมูลหา Manager ได้ตามปกติ)
