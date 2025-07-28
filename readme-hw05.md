# วิธีการติดตั้งและใช้งาน Superset & ClickHouse บน macOS

---

## a. วิธีการติดตั้งและเปิดใช้งาน ClickHouse

1. ติดตั้ง ClickHouse Server และ Client ด้วย Homebrew:
   ```bash
   brew install clickhouse
เริ่มต้น ClickHouse Server:

bash
คัดลอก
แก้ไข
clickhouse-server
ในหน้าต่าง Terminal อื่น ลองเชื่อมต่อด้วย client:

bash
คัดลอก
แก้ไข
clickhouse-client
b. วิธีนำเข้าข้อมูลจากการบ้านที่ 4 (HW04)
เตรียมไฟล์ CSV หรือไฟล์ข้อมูลจากการบ้านที่ 4

สร้างตารางใน ClickHouse:

sql
คัดลอก
แก้ไข
CREATE TABLE nypd_complaint_raw_all_string (
  CMPLNT_NUM String,
  ADDR_PCT_CD String,
  BORO_NM String,
  CMPLNT_FR_DT String,
  CMPLNT_FR_TM String,
  CMPLNT_TO_DT String,
  CMPLNT_TO_TM String,
  CRM_ATPT_CPTD_CD String,
  HADEVELOPT String,
  HOUSING_PSA String,
  JURISDICTION_CODE String,
  JURIS_DESC String,
  KY_CD String,
  LAW_CAT_CD String,
  LOC_OF_OCCUR_DESC String,
  OFNS_DESC String,
  PARKS_NM String,
  PATROL_BORO String,
  PD_CD String,
  PD_DESC String,
  PREM_TYP_DESC String,
  RPT_DT String,
  STATION_NAME String,
  SUSP_AGE_GROUP String,
  SUSP_RACE String,
  SUSP_SEX String,
  TRANSIT_DISTRICT String,
  VIC_AGE_GROUP String,
  VIC_RACE String,
  VIC_SEX String,
  X_COORD_CD String,
  Y_COORD_CD String,
  Latitude String,
  Longitude String,
  Lat_Lon String,
  `New Georeferenced Column` String
) ENGINE = MergeTree
ORDER BY CMPLNT_NUM;
นำเข้าไฟล์ CSV:

bash
คัดลอก
แก้ไข
clickhouse client --query="INSERT INTO nypd_complaint_raw_all_string FORMAT CSVWithNames" < ~/Downloads/NYPD_Complaint.csv
c. วิธีติดตั้งและเปิดใช้งาน Apache Superset
สร้าง virtual environment:

bash
คัดลอก
แก้ไข
python3 -m venv venv
source venv/bin/activate
อัปเกรด pip และติดตั้ง Superset:

bash
คัดลอก
แก้ไข
pip install --upgrade pip setuptools wheel
pip install apache-superset
ตั้งค่าระบบ Superset:

bash
คัดลอก
แก้ไข
superset db upgrade
export FLASK_APP=superset
superset fab create-admin   # สร้าง admin user
superset init
รัน Superset:

bash
คัดลอก
แก้ไข
superset run -p 8088 --with-threads --reload --debugger
จากนั้นเปิดเบราว์เซอร์ที่ http://localhost:8088

d. วิธีเชื่อม Superset กับ ClickHouse
ติดตั้งไดรเวอร์ ClickHouse:

bash
คัดลอก
แก้ไข
pip install clickhouse-connect
ในหน้า Superset ไปที่:
Data → Databases → + Database

กรอก URI สำหรับเชื่อมต่อ ClickHouse:

sql
คัดลอก
แก้ไข
clickhouse+http://default:@localhost:8123/default
กด Test Connection แล้วกด Save

e. วิธีนำเสนอข้อมูลจากข้อ b. บน Dashboard ของ Superset
หมายเหตุ: ใช้ dataset Web_analytics แทนเนื่องจากข้อมูล NYPD บาง field ไม่สามารถ visualize ได้โดยตรง

ไปที่ Data → Datasets → + Dataset

เลือก Database ที่เชื่อมไว้ (ClickHouse)

เลือกตาราง: Web_analytics

ตั้งชื่อ Dataset แล้วกด Save

สร้าง Visualization:

ไปที่ Charts → + Chart

เลือก Dataset: Web_analytics

เลือก Visualization: Pie Chart

ตั้งค่า:

Dimension → browser

Metric → COUNT(*)

กด Run แล้ว Save

สร้าง Dashboard:

ไปที่ Dashboards → + Dashboard

ตั้งชื่อเช่น “HW04 Dashboard” แล้วกด Create

เพิ่ม Chart ที่สร้างไว้ลงใน Dashboard

📊 ผลลัพธ์:
ได้ Dashboard แสดงสัดส่วนการใช้งานเบราว์เซอร์ต่าง ๆ จาก dataset ตัวอย่าง


f. บทสรุปการใช้งาน
Superset เป็นเครื่องมือ BI ที่ทรงพลังและฟรี ใช้สร้าง Visualization และ Dashboard ได้อย่างรวดเร็ว

ClickHouse เป็นระบบฐานข้อมูล columnar ที่เร็วและเหมาะสำหรับงาน analytics

การเชื่อม Superset กับ ClickHouse และการนำเข้าข้อมูลจากการบ้าน HW04 เป็นกระบวนการที่ง่ายต่อการเรียนรู้

เหมาะอย่างยิ่งสำหรับ:

นักเรียน/นักศึกษาในสาย Data

นักวิเคราะห์ข้อมูล

ผู้เริ่มต้นฝึกใช้งานระบบ BI

📌 หากคุณใช้ ClickHouse ผ่าน Docker อย่าลืมตรวจสอบพอร์ตและ URL ในการเชื่อมต่อให้ตรงกันด้วย

url สำหรับ github.io https://jarkapat.github.io/Jarkpat/
