```mermaid
flowchart TD
    subgraph Kernel [1️⃣ ฝั่งต้นทาง: OS Kernel]
        direction TB
        F("ฟังก์ชัน security_socket_connect<br/>(มีตัวแปรส่งมา 3 ตัว)")
        V0("ตำแหน่งที่ 0: sock<br/>(เราไม่สนใจ ❌)")
        V1("ตำแหน่งที่ 1: sockaddr<br/>(ข้อมูล IP/Port ✅)")
        V2("ตำแหน่งที่ 2: addrlen<br/>(เราไม่สนใจ ❌)")
        F --> V0
        F --> V1
        F --> V2
    end

    subgraph YAML [2️⃣ ฝั่งคนกลาง: ไฟล์ YAML]
        direction TB
        R("เขียนกฎ: <b>index: 1</b><br/>สั่งให้ล้วงเอาข้อมูลจากตำแหน่ง 1")
        A("Tetragon หยิบข้อมูลมา 1 ชิ้น<br/>จับใส่ 'กล่องพัสดุ (Array)'")
        R --> A
    end

    subgraph JSON [3️⃣ ฝั่งปลายทาง: หน้าจอ JSON / Wazuh]
        direction TB
        B("กล่องพัสดุชื่อ 'args'")
        I0("ตำแหน่งที่ 0<br/>(คอมพิวเตอร์นับของชิ้นแรกเป็นเลข 0 เสมอ)")
        D("sockaddr_arg<br/>- addr: 172.24.0.3<br/>- port: 3306")
        B --> I0
        I0 --> D
    end

    V1 ==>|ดึงข้อมูลตัวที่ 1 เท่านั้น| R
    A ==>|ส่งกล่องข้อมูล| B

    classDef darkBox fill:#121212,stroke:#00ff66,stroke-width:2px,color:#00ff66;
    class F,V0,V1,V2,R,A,B,I0,D darkBox;
    linkStyle default stroke:#00ff66,stroke-width:2px;
