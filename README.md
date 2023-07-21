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
tool : excel เพราะช่วยให้เราเห็น format ของแต่ละ column ได้ง่าย และ fliter ข้อมูลได้อย่างรวดเร็ว

data cleaning
1. Check null values

จากการ filter พบมีข้อมูลบางส่วนหายไปใน 6 column นี้
```
start_station_name 
start_station_id
end_station_name
end_station_id 
end_lat 
end_lng
```
filter หา row ที่มีข้อมูลเพียง 1 column จากใน 2 columnนี้ start_station_name หรือ start_station_id ได้ 0 row

filter หา row ที่มีข้อมูลเพียง 1 column จากใน 2 columnนี้ end_station_name หรือ end_station_id ได้ 0 row

แสดงว่า station_name และ station_id ข้อมูลหายไปพร้อมกันเสมอ เราไม่สามารถหา station_id แล้ว insert station_name เองได้ จึงจำเป็นต้องลบ row เหล่านี้ทิ้งไป 

นอกจากนี้พบว่า row ไหนที่ไม่มี end_lat กับ end_lng จะไม่มี end_station เสมอ

2. Start and end date-time
เวลา start ต้องเกิดก่อน end 
   

## Analyze
1.import data tool ที่ใช้ Postgresql เพราะ sql สามารถqueryข้อมูลจำนวนมากได้รวดเร็วกว่า excel 

สร้าง table ใน postgres ก่อน

```
CREATE TABLE IF NOT EXISTS bike_info
(
    ride_id varchar(16)  PRIMARY KEY  NOT NULL,
    rideable_type text  NOT NULL,
    started_at timestamp without time zone NOT NULL,
    ended_at timestamp without time zone NOT NULL,
    start_station_name varchar(100) NOT NULL,
    start_station_id varchar(100) NOT NULL,
    end_station_name varchar(100) NOT NULL,
    end_station_id varchar(100) NOT NULL,
    start_lat numeric NOT NULL,
    start_lng numeric NOT NULL,
    end_lat numeric NOT NULL,
    end_lng numeric NOT NULL,
    member_casual varchar(100) NOT NULL,
)
```

import ข้อมูลเข้า postgres ใช้คำสั่งนี้ไปเรื่อยๆ แต่เปลี่ยนชื่อไฟล์จนครบ12เดือน
```
\copy bike_info FROM '202201-divvy-tripdata.csv' WITH (FORMAT csv, HEADER true, DELIMITER ',');
```
