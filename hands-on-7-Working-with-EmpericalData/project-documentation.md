# โครงสร้างโปรเจค EGAT Advanced Statistics

เอกสารฉบับนี้อธิบายโครงสร้างของโปรเจค EGAT Advanced Statistics ประกอบด้วยการอธิบายความสัมพันธ์ระหว่างไฟล์ Python ต่างๆ ในโครงการ และการไหลของข้อมูลระหว่างไดเรกทอรี

## ไดเรกทอรีหลัก

โปรเจคนี้มีไดเรกทอรีหลักสำหรับเก็บข้อมูลในขั้นตอนต่างๆ ดังนี้:

1. **/scada_data/** - เก็บไฟล์ ZIP ต้นฉบับที่ดาวน์โหลดจาก NEMWEB
2. **/temp_extract/** - โฟลเดอร์ชั่วคราวสำหรับสกัดข้อมูลจากไฟล์ ZIP
3. **/extracted_files/** - เก็บไฟล์ CSV ที่สกัดได้ โดยแยกตามวันที่หรือชื่อไฟล์ต้นฉบับ
4. **/extracted_files_consolidated/** - เก็บไฟล์ CSV ที่สกัดได้ทั้งหมดรวมไว้ในโฟลเดอร์เดียว
5. **/analysis_results/** - เก็บผลการวิเคราะห์ข้อมูล เช่น กราฟ รายงาน และไฟล์ JSON
6. **/profiling_results/** - เก็บผลการทำ data profiling และการเตรียมข้อมูลสำหรับโมเดล N-BEATS

## กลุ่มไฟล์ตามหน้าที่การทำงาน

โปรเจคประกอบด้วยไฟล์ Python หลายไฟล์ที่แบ่งตามหน้าที่การทำงานได้ดังนี้:

### 1. กลุ่มดาวน์โหลดและสกัดข้อมูล
- **download_nemweb.py** - ดาวน์โหลดไฟล์ ZIP จาก NEMWEB ไปยัง `/scada_data/`
- **extract_nested_zips.py** - สกัดไฟล์ ZIP รวมถึง ZIP ซ้อนใน `/scada_data/` ไปยัง `/temp_extract/`
- **extract_all_and_consolidate.py** - สกัดไฟล์ ZIP ทั้งหมดและรวมไว้ใน `/extracted_files_consolidated/`
- **extract_and_analyze.py** - สกัดและวิเคราะห์ข้อมูลในคราวเดียว
- **extract_and_keep.py** - สกัดและเก็บไฟล์ไว้ใน `/extracted_files/`
- **extract_only.py** - สกัดไฟล์เท่านั้น ไปยัง `/extracted_files/`

### 2. กลุ่มวิเคราะห์ข้อมูล
- **analyze_extracted_data.py** - วิเคราะห์ข้อมูลที่สกัดได้จาก `/extracted_files/`
- **analyze_extracted_data_thai.py** - เวอร์ชันภาษาไทยของ analyze_extracted_data.py
- **analyze_scada_data.py** - วิเคราะห์ข้อมูล SCADA โดยเฉพาะ
- **analyze_zip_thai.py** - วิเคราะห์ไฟล์ ZIP โดยตรง (มีฟังก์ชันสกัดในตัว) โดยอ่านจาก `/scada_data/`
- **power_industry_analysis.py** - วิเคราะห์ข้อมูลเฉพาะสำหรับอุตสาหกรรมไฟฟ้า
- **show_csv_structure.py** - แสดงโครงสร้างของไฟล์ CSV

### 3. กลุ่ม Data Profiling
- **data_profiling.py** - ทำ data profiling และเตรียมข้อมูลสำหรับโมเดล N-BEATS โดยอ่านจาก `/extracted_files_consolidated/`
- **data_quality_report.py** - สร้างรายงานคุณภาพข้อมูล

### 4. กลุ่มนำเข้าข้อมูลสู่ SQL
- **split_csv.py** - แบ่งไฟล์ CSV ขนาดใหญ่เป็นหลายไฟล์ย่อย
- **import_chunks_to_sql.py** - นำเข้าข้อมูลเป็นชิ้นๆ ไปยัง SQL
- **import_scada_to_sql.py**, **import_scada_to_sql_fixed.py** - นำเข้าข้อมูล SCADA ไปยัง SQL

### 5. กลุ่มทดสอบ
- **test_connection.py** - ทดสอบการเชื่อมต่อ
- **run_test.py**, **run_test_with_real_data.py**, **test_only_part3.py**, **test_report_generation.py** - ไฟล์ทดสอบต่างๆ

### 6. ไฟล์เสริม
- **create_dummy_images.py** - สร้างรูปภาพจำลอง

## ความสัมพันธ์ระหว่างไฟล์และการไหลของข้อมูล

ไฟล์ Python ในโปรเจคมีความสัมพันธ์กันตามลำดับการทำงาน ดังนี้:

1. **download_nemweb.py** -> **extract_nested_zips.py** -> **extract_all_and_consolidate.py** -> **extract_only.py**
   - เป็นลำดับหลักในการดาวน์โหลดและสกัดข้อมูล

2. **extract_nested_zips.py** -> **extract_and_analyze.py**, **extract_and_keep.py**
   - ทางเลือกอื่นในการสกัดข้อมูล

3. **extract_nested_zips.py** -> **split_csv.py** -> **import_chunks_to_sql.py** -> **import_scada_to_sql.py** -> **import_scada_to_sql_fixed.py**
   - การนำข้อมูลที่สกัดได้เข้าสู่ฐานข้อมูล

4. **extract_and_analyze.py**, **extract_and_keep.py**, **extract_only.py** -> **analyze_extracted_data.py** -> **analyze_extracted_data_thai.py** -> **analyze_scada_data.py**
   - การวิเคราะห์ข้อมูลที่สกัดได้

5. **download_nemweb.py** -> **analyze_zip_thai.py**
   - การวิเคราะห์ไฟล์ ZIP โดยตรง (ไม่ผ่าน extract_nested_zips.py)

6. **analyze_extracted_data.py** -> **power_industry_analysis.py** -> **data_profiling.py** -> **data_quality_report.py**
   - การวิเคราะห์เชิงลึกและการทำ data profiling

7. **test_connection.py** -> **run_test.py** -> **run_test_with_real_data.py**, **test_only_part3.py**, **test_report_generation.py**
   - การทดสอบระบบ

## การไหลของข้อมูลระหว่างไดเรกทอรี

1. ข้อมูลจาก NEMWEB ถูกดาวน์โหลดโดย **download_nemweb.py** ไปยัง `/scada_data/`
2. ไฟล์ ZIP ใน `/scada_data/` ถูกสกัดโดยกลุ่มไฟล์สกัดข้อมูล:
   - **extract_nested_zips.py** สกัดไปยัง `/temp_extract/`
   - **extract_all_and_consolidate.py** สกัดและรวมไปยัง `/extracted_files_consolidated/`
   - **extract_and_keep.py** สกัดและเก็บไว้ใน `/extracted_files/`

3. ไฟล์ที่สกัดแล้วถูกวิเคราะห์:
   - **analyze_extracted_data.py** และไฟล์อื่นๆ ในกลุ่มวิเคราะห์อ่านจาก `/extracted_files/` หรือ `/extracted_files_consolidated/`
   - ผลลัพธ์การวิเคราะห์ถูกบันทึกไปยัง `/analysis_results/`

4. ข้อมูลถูกทำ profiling:
   - **data_profiling.py** อ่านข้อมูลจาก `/extracted_files_consolidated/`
   - ผลลัพธ์ถูกบันทึกไปยัง `/profiling_results/`

5. การนำเข้าข้อมูลสู่ SQL:
   - ไฟล์ CSV ที่ได้จากการสกัดถูกแบ่งโดย **split_csv.py** (ถ้าจำเป็น)
   - **import_chunks_to_sql.py** และไฟล์อื่นๆ นำข้อมูลเข้าสู่ฐานข้อมูล SQL

## ข้อสังเกตพิเศษ

1. **analyze_zip_thai.py** เป็นไฟล์ที่ทำงานแบบเอกเทศ โดยอ่านไฟล์ ZIP จาก `/scada_data/` โดยตรง มีฟังก์ชันสกัดข้อมูลในตัว และสามารถจัดการกับ nested ZIP ได้

2. ข้อมูลในโฟลเดอร์ `/temp_extract/` เป็นไฟล์ชั่วคราวที่มักจะถูกลบหลังจากการประมวลผลเสร็จสิ้น

3. ข้อมูลใน `/extracted_files/` จะถูกจัดเก็บแยกตามวัน/ชื่อไฟล์ ในขณะที่ `/extracted_files_consolidated/` จะเก็บไฟล์ทั้งหมดรวมกันในโฟลเดอร์เดียว

4. การนำข้อมูลเข้าสู่ SQL มีทั้งแบบปกติ (**import_scada_to_sql.py**) และแบบที่มีการแก้ไขปัญหาบางอย่าง (**import_scada_to_sql_fixed.py**)

---

เอกสารนี้จัดทำขึ้นเพื่อเป็นแนวทางในการทำความเข้าใจโครงสร้างและความสัมพันธ์ของไฟล์ต่างๆ ในโปรเจค EGAT Advanced Statistics สำหรับผู้ที่ต้องการศึกษาหรือพัฒนาต่อยอดจากโปรเจคนี้
