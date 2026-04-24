# 🐟 Smart Aquaculture System

> ระบบบริหารจัดการบ่อเพาะเลี้ยงสัตว์น้ำอัจฉริยะ  
> ตรวจสอบค่าเซนเซอร์แบบ Real-time · แจ้งเตือนอัตโนมัติ · จัดการผ่าน Web Dashboard

---

## สารบัญ (Table of Contents)

- [ภาพรวมระบบ](#ภาพรวมระบบ)
- [Tech Stack & Tools](#tech-stack--tools)
- [โครงสร้างโปรเจกต์](#โครงสร้างโปรเจกต์)
- [คู่มือการใช้งาน](#คู่มือการใช้งาน-user-manual)
- [การสร้าง Ticket และจัดการเวอร์ชัน](#การสร้าง-ticket-และจัดการเวอร์ชัน)

---

##  ภาพรวมระบบ

**Smart Aquaculture System** คือแพลตฟอร์มสำหรับตรวจสอบและบริหารจัดการบ่อเพาะเลี้ยงสัตว์น้ำผ่าน Web Dashboard โดยรับข้อมูลจาก IoT Device (เช่น ESP32) แล้วแสดงผลแบบ Real-time พร้อมระบบแจ้งเตือนเมื่อค่าเซนเซอร์ผิดปกติ

### ฟีเจอร์หลัก

- 📊 **Dashboard Real-time** — แสดงค่า อุณหภูมิ, pH, ความขุ่น, ระดับน้ำ จากทุกตู้
- 🔔 **Alert System** — แจ้งเตือนอัตโนมัติเมื่อค่าเกินขีดจำกัดที่ตั้งไว้
- 🐠 **Tank Management** — เพิ่ม/ลบ/แก้ไขตู้ พร้อม Preset สำหรับปลาแต่ละสายพันธุ์
- 🩺 **Device Health** — ตรวจสอบสถานะ Sensor, Pump, Feeder, CCTV
- 📈 **History & Report** — ดูกราฟข้อมูลย้อนหลังและส่งออก Report
- 🔐 **Authentication** — ระบบ Login พร้อม Rate Limit ป้องกัน Brute Force
- 🧹 **Auto Purge** — ลบ Log เก่าอัตโนมัติผ่าน Cron Job

---

##  Tech Stack & Tools

| Layer | Technology |
|---|---|
| **Backend** | PHP 8.2, PDO (MySQL) |
| **Frontend** | HTML5 + Tailwind CSS (CDN) |
| **Database** | MySQL 8.0 |
| **Container** | Docker, Docker Compose |
| **Admin DB** | phpMyAdmin |
| **IoT Protocol** | HTTP POST (REST API) |

---

##  โครงสร้างโปรเจกต์

```
SmartAquacultureSystem/
├── admin/                   # Web Dashboard (HTML)
│   ├── index.html           # หน้าหลัก Dashboard
│   ├── login.html           # หน้า Login
│   ├── tank_detail.html     # รายละเอียดตู้
│   ├── history.html         # ประวัติข้อมูลเซนเซอร์
│   ├── alert_log.html       # ประวัติการแจ้งเตือน
│   ├── device_health.html   # สถานะอุปกรณ์
│   ├── report.html          # รายงาน
│   ├── settings.html        # ตั้งค่าระบบ
│   └── simulator.html       # จำลองข้อมูล IoT
├── api/                     # REST API Endpoints (PHP)
│   ├── auth.php             # POST: Login / Authentication
│   ├── logout.php           # POST: Logout
│   ├── get_user.php         # GET: ข้อมูล User ปัจจุบัน
│   ├── insert_sensor.php    # POST: รับค่าจาก IoT Device
│   ├── get_latest.php       # GET: ค่าเซนเซอร์ล่าสุด
│   ├── get_history.php      # GET: ประวัติข้อมูล
│   ├── get_stats.php        # GET: สถิติ
│   ├── get_events.php       # GET: Event/Alert Log
│   ├── get_device_status.php# GET: สถานะ Device
│   ├── tank_create.php      # POST: สร้างตู้ใหม่
│   ├── tank_delete.php      # DELETE: ลบตู้ (Soft Delete)
│   ├── update_settings.php  # POST: อัปเดตการตั้งค่า
│   ├── feed_now.php         # POST: สั่งให้อาหารทันที
│   ├── simulate_device_error.php # POST: จำลอง Device Error
│   ├── seed_demo_data.php   # POST: เพิ่มข้อมูลตัวอย่าง
│   └── health.php           # GET: ตรวจสอบสถานะ Server
├── cron/
│   └── purge_old_logs.php   # ลบ Log เก่าอัตโนมัติ
├── migrations/              # SQL Migration Scripts
│   ├── 001_index_and_soft_delete.sql
│   ├── 002_rename_species.sql
│   └── 003_add_hardware_and_sensor_configs.sql
├── db.php                   # Database Connection (PDO)
├── docker-compose.yml       # Docker Services Config
├── Dockerfile               # PHP 8.2 + Apache Image
└── .env                     # Environment Variables
```

---

##  คู่มือการใช้งาน (User Manual)

### 1. สิ่งที่ต้องมีก่อนติดตั้ง

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) (รองรับ Windows, macOS, Linux)
- Git

### 2. การติดตั้ง

**Step 1: Clone โปรเจกต์**

```bash
git clone https://github.com/your-username/SmartAquacultureSystem.git
cd SmartAquacultureSystem
```

**Step 2: ตั้งค่า Environment**

```bash
cp .env.example .env
# แก้ไขค่าใน .env ตามต้องการ
```

ตัวอย่างค่าใน `.env`:

```env
MYSQL_ROOT_PASSWORD=root
MYSQL_DATABASE=smartaqua
MYSQL_USER=user
MYSQL_PASSWORD=password

DB_HOST=db
DB_PORT=3306
DB_NAME=smartaqua
DB_USER=user
DB_PASSWORD=password

CRON_TOKEN=your-secret-token-here
```

**Step 3: รัน Docker**

```bash
docker compose up -d
```

**Step 4: รัน Migration (ครั้งแรก)**

```bash
# ผ่าน phpMyAdmin ที่ http://localhost:8081
# หรือผ่าน Docker
docker exec -i db mysql -uuser -ppassword smartaqua < migrations/001_index_and_soft_delete.sql
docker exec -i db mysql -uuser -ppassword smartaqua < migrations/002_rename_species.sql
docker exec -i db mysql -uuser -ppassword smartaqua < migrations/003_add_hardware_and_sensor_configs.sql
```

**Step 5: เข้าใช้งาน**

| Service | URL |
|---|---|
| 🌐 Web Dashboard | http://localhost:8080 |
| 🗄️ phpMyAdmin | http://localhost:8081 |
| 🔍 Health Check | http://localhost:8080/api/health.php |

---

### 3. การส่งข้อมูลจาก IoT Device

ส่ง HTTP POST ไปที่ `http://<server>:8080/api/insert_sensor.php`

```json
{
  "tank_id": 1,
  "temperature": 28.5,
  "ph": 7.2,
  "turbidity": 15.3,
  "water_level": 85.0
}
```

### 4. Preset สายพันธุ์สัตว์น้ำ

เมื่อสร้างตู้ใหม่ สามารถเลือก Preset ได้ดังนี้:

| Preset | สายพันธุ์ |
|---|---|
| `default` | ค่าเริ่มต้นทั่วไป |
| `tilapia` | ปลานิล |
| `shrimp` | กุ้ง |
| `catfish` | ปลาดุก |
| `blank` | ไม่มี Preset |

### 5. Auto Purge Log (Cron Job)

```bash
# รันด้วยตนเอง
docker exec php-apache php /var/www/html/cron/purge_old_logs.php

# ตั้งเวลาอัตโนมัติ (ทุกคืน 03:00)
0 3 * * * docker exec php-apache php /var/www/html/cron/purge_old_logs.php >> /var/log/smartaqua-purge.log 2>&1
```

| Log ประเภท | เก็บไว้นานเท่าไหร่ |
|---|---|
| sensor_logs | 90 วัน |
| event_logs | 365 วัน |
| login_logs | 180 วัน |
| Soft-deleted tanks | 30 วัน (จากนั้น Hard Delete) |

---

##  การสร้าง Ticket และจัดการเวอร์ชัน

### วิธีสร้าง Issue / Ticket

1. ไปที่ **GitHub Repository** → แท็บ **Issues**
2. กด **New Issue**
3. เลือก Template ที่เหมาะสม:
   - 🐛 **Bug Report** — พบข้อผิดพลาด
   - ✨ **Feature Request** — ขอฟีเจอร์ใหม่
   - 📋 **Task** — งานทั่วไป

### การตั้งชื่อ Branch

```
feature/ชื่อฟีเจอร์    เช่น  feature/add-export-csv
bugfix/ชื่อบัก         เช่น  bugfix/fix-auth-rate-limit
hotfix/ชื่อปัญหา       เช่น  hotfix/sensor-insert-null
```

### Migration Convention

ไฟล์ Migration ตั้งชื่อตามรูปแบบ: `NNN_คำอธิบาย.sql`

```
001_index_and_soft_delete.sql
002_rename_species.sql
003_add_hardware_and_sensor_configs.sql
```

> **หมายเหตุ:** Migration ทุกไฟล์ออกแบบให้รันซ้ำได้ (Idempotent) ปลอดภัยหากรันซ้ำ

---

## Database Schema (สรุป)

```
tanks              — ข้อมูลตู้ (soft delete ด้วย deleted_at)
tank_settings      — การตั้งค่าขีดจำกัดค่าเซนเซอร์
sensor_logs        — ค่าเซนเซอร์ที่รับจาก IoT
sensor_configs     — การตั้งค่าเซนเซอร์แต่ละตัว
devices            — อุปกรณ์ (pump, feeder, cctv)
event_logs         — Log การแจ้งเตือน
users              — บัญชีผู้ใช้ (+ session_token index)
login_logs         — ประวัติการ Login
```

---

