# Cyclistic_bike_share_analysis
 Cyclistic bike-share analysis case study from Google Data Analytics course
 
 ## Scenario
 บริษัท Cyclistic ต้องการออกแบบกลยุทธ์ทางการตลาดที่มุ่งเปลี่ยนผู้ขับขี่ทั่วไปให้เป็นสมาชิกรายปี เพื่อที่จะทำเช่นนั้นทีมนักวิเคราะห์การตลาดจำเป็นต้องเข้าใจให้ดียิ่งขึ้นว่าสมาชิกรายปีและผู้โดยสารทั่วไปแตกต่างกันอย่างไร เพราะเหตุใดผู้ขับขี่ทั่วไปจะซื้อสมาชิก และสื่อดิจิทัลจะส่งผลต่อกลยุทธ์ทางการตลาดของพวกเขาอย่างไร 

## Ask 
1. สมาชิกรายปีและนักปั่นทั่วไปใช้จักรยาน Cyclistic แตกต่างกันอย่างไร?
2. เหตุใดผู้ขับขี่ทั่วไปจึงซื้อสมาชิกรายปีแบบ Cyclistic
3. Cyclistic จะใช้สื่อดิจิทัลเพื่อโน้มน้าวผู้ขับขี่ทั่วไปให้เป็นสมาชิกได้อย่างไร

## Prepare
1.เช็คคุณภาพของข้อมูล

โดยข้อมูลที่นำมานั้นมาจากภายในบริษัทเองจึงเชื่อถือได้ ข้อมูลนั้นครอบคลุมรายละเอียดการเดินทาง วันเวลา ประเภทพาหนะ และประเภทสมาชิก และข้อมูลมีการอัพเดททุกเดือน ข้อมูลไม่มีการ bias เพราะได้ลบข้อมูลที่พนักงานบริษัทใช้งานเองออกหมดแล้ว นอกจากนี้จำนวนข้อมูลนั้นเพียงพอในการวิเคราะห์เพราะเป็นข้อมูลทั้งpopulationของผู้ขับขี่ทั้งหมดที่ใช้บริการCyclistic

2.licence and privacy

 data นี้เป็น public data ของบริษัทสมมติ Cyclistic ดูข้อมูลได้ที่ [Divvy](https://divvy-tripdata.s3.amazonaws.com/index.html) ถูกจัดทำภายใต้ [licenceนี้](https://ride.divvybikes.com/data-license-agreement) และไม่เปิดเผยข้อมูลส่วนบุคคลของผู้ขับขี่ ทำให้เราไม่มีสามารถวิเคราะห์ที่อยู่อาศัยของผู้ขับขี่กับตำแหน่งสถานีเช่าจักรยานได้

## Process
1.tool ที่ใช้ Postgresql
เพราะ sql สามารถqueryข้อมูลจำนวนมากได้รวดเร็วกว่า excel 

2.import data

สร้าง table ใน postgres ก่อน

```
CREATE TABLE IF NOT EXISTS bike_info
(
    ride_id varchar(16)  PRIMARY KEY  NOT NULL,
    rideable_type text  NOT NULL,
    started_at timestamp without time zone,
    ended_at timestamp without time zone,
    start_station_name varchar(100),
    start_station_id varchar(100) ,
    end_station_name varchar(100) ,
    end_station_id varchar(100) ,
    start_lat numeric,
    start_lng numeric,
    end_lat numeric,
    end_lng numeric,
    member_casual varchar(100) ,
)
```

import ข้อมูลเข้า postgres ใช้คำสั่งนี้ไปเรื่อยๆ แต่เปลี่ยนชื่อไฟล์จนครบ12เดือน
```
\copy bike_info FROM '202201-divvy-tripdata.csv' WITH (FORMAT csv, HEADER true, DELIMITER ',');
```

data cleaning
1.มีข้อมูลบางส่วนหายไป

```
start_station_name 
start_station_id
end_station_name
end_station_id 
end_lat 
end_lng
```

