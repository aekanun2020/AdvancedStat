# การวิเคราะห์กราฟเพื่อบ่งชี้ Seasonality และการกำหนดพารามิเตอร์สำหรับโมเดลอนุกรมเวลา

## 1. การใช้กราฟในการวิเคราะห์ Time Series

การวิเคราะห์กราฟเป็นขั้นตอนสำคัญในการระบุรูปแบบและองค์ประกอบฤดูกาลในข้อมูลอนุกรมเวลา ช่วยในการกำหนดพารามิเตอร์ที่เหมาะสมสำหรับโมเดลพยากรณ์ต่างๆ

### 1.1 องค์ประกอบหลักของอนุกรมเวลา

อนุกรมเวลาประกอบด้วยองค์ประกอบหลัก 4 ประการ:

1. **แนวโน้ม (Trend)** - การเปลี่ยนแปลงในระยะยาว แสดงทิศทางการเคลื่อนไหวของข้อมูลโดยรวม
2. **ฤดูกาล (Seasonality)** - รูปแบบที่เกิดซ้ำในระยะเวลาที่แน่นอนและคาดการณ์ได้
3. **วัฏจักร (Cyclical)** - รูปแบบขึ้นลงในระยะยาว แต่ไม่มีความถี่ที่แน่นอน มักเกี่ยวข้องกับวัฏจักรทางเศรษฐกิจ
4. **ความไม่สม่ำเสมอ (Irregular)** - ความผันผวนแบบสุ่มที่ไม่สามารถอธิบายด้วยองค์ประกอบอื่น

### 1.2 กราฟและการวิเคราะห์ในหลายมาตราส่วนเวลา (Multi-scale Time Analysis)

การวิเคราะห์ข้อมูลในหลายมาตราส่วนเวลาช่วยให้เห็นรูปแบบที่แตกต่างกัน การพิจารณากราฟในระดับต่างๆ จะเผยให้เห็นลักษณะที่หลากหลาย โดยกราฟระยะยาว (หลายปี) จะแสดงแนวโน้ม วัฏจักรเศรษฐกิจ และการเปลี่ยนแปลงฤดูกาลรายปี ส่วนกราฟรายปีจะแสดงรูปแบบฤดูกาลภายในปีและความแตกต่างระหว่างเดือน กราฟรายเดือนช่วยให้เห็นรูปแบบรายสัปดาห์และรายวัน ขณะที่กราฟรายสัปดาห์จะแสดงรูปแบบรายวันได้ละเอียดยิ่งขึ้น และกราฟรายวันเผยให้เห็นรูปแบบรายชั่วโมงได้อย่างชัดเจน

### 1.3 เทคนิคการแสดงภาพข้อมูลสมัยใหม่

เทคนิคการแสดงภาพข้อมูลสมัยใหม่มีบทบาทสำคัญในการวิเคราะห์อนุกรมเวลา เทคนิค Heatmap ช่วยแสดงความเข้มของข้อมูลตามเวลา ทำให้เห็นรูปแบบฤดูกาลได้ชัดเจน เช่น การแสดงข้อมูลรายชั่วโมงเป็นแถวและรายวันเป็นคอลัมน์ ส่วน Calendar Plots นำเสนอข้อมูลในรูปแบบปฏิทิน ช่วยให้เห็นรูปแบบตามวันในสัปดาห์หรือวันในเดือนได้ง่าย นอกจากนี้ยังมี Interactive Time Series Plots ซึ่งเป็นกราฟแบบโต้ตอบที่ผู้ใช้สามารถซูมเข้า-ออกและเลือกช่วงเวลาได้ตามต้องการ และ Seasonal Subseries Plots ที่แยกแสดงข้อมูลตามฤดูกาล เช่น การแสดงข้อมูลแต่ละเดือนเรียงต่อกันหลายปีเพื่อเปรียบเทียบรูปแบบที่เกิดซ้ำ

## 2. การบ่งชี้รูปแบบฤดูกาล (Seasonality)

### 2.1 ลักษณะของฤดูกาลที่ชัดเจน

ฤดูกาลที่ชัดเจนจะแสดงลักษณะดังนี้:
- มีรูปแบบซ้ำในช่วงเวลาคงที่และสม่ำเสมอ
- ความสูงของจุดสูงสุดและต่ำสุดในแต่ละรอบใกล้เคียงกัน
- มีจุดเปลี่ยนที่คาดเดาได้ (เช่น จุดสูงสุด-ต่ำสุดเกิดในช่วงเวลาเดียวกันของวัน/สัปดาห์/เดือน)

**ตัวอย่างรูปแบบฤดูกาลที่ชัดเจน:**
- **รายชั่วโมง**: ค่าสูงในช่วงกลางวัน (6-18 น.) และต่ำในช่วงกลางคืน
- **รายวัน**: ค่าสูงในวันธรรมดา (จ-ศ) และต่ำในวันหยุด (ส-อา)
- **รายเดือน**: ค่าสูงในฤดูร้อน (มี.ค.-พ.ค.) และฤดูหนาว (พ.ย.-ม.ค.) และต่ำในฤดูฝน (มิ.ย.-ต.ค.)

### 2.2 ลักษณะของฤดูกาลที่ไม่ชัดเจน

ฤดูกาลที่ไม่ชัดเจนจะแสดงลักษณะดังนี้:
- มีความผันผวนสูง ทำให้ยากที่จะระบุรูปแบบที่ซ้ำกัน
- จุดสูงสุดและต่ำสุดเกิดในช่วงเวลาที่ไม่แน่นอน
- ความสูงของจุดสูงสุดและต่ำสุดมีความแตกต่างมากในแต่ละรอบ
- มีเหตุการณ์พิเศษบ่อยที่ทำให้รูปแบบถูกรบกวน

### 2.3 การใช้กราฟค่าเฉลี่ย

กราฟค่าเฉลี่ยช่วยให้เห็นรูปแบบฤดูกาลได้ชัดเจนขึ้น:
- **แบ่งตามช่วงเวลา**: คำนวณค่าเฉลี่ยแยกตามชั่วโมง, วันในสัปดาห์, เดือน
- **ค่าเฉลี่ยเคลื่อนที่ (Moving Average)**: กรองความผันผวนระยะสั้นออกเพื่อให้เห็นรูปแบบระยะยาวชัดเจนขึ้น
- **Box Plots**: แสดงการกระจายตัวของข้อมูลในแต่ละช่วงเวลา

### 2.4 เทคนิคการวิเคราะห์ฤดูกาลขั้นสูง (ปี 2024)

เทคนิคสมัยใหม่ในการวิเคราะห์ฤดูกาลได้พัฒนาก้าวหน้าไปมากในปี 2024 STL Decomposition (Seasonal-Trend decomposition using LOESS) เป็นเทคนิคการแยกองค์ประกอบที่ยืดหยุ่น รองรับฤดูกาลที่มีการเปลี่ยนแปลง ในปัจจุบันได้มีการพัฒนา OneShotSTL ซึ่งเป็นเวอร์ชันปรับปรุงที่ทำงานได้เร็วกว่าและเหมาะสำหรับการวิเคราะห์แบบเรียลไทม์ 

Seasonal-Trend-Dispersion Decomposition (STD) ได้ขยายแนวคิดของ STL โดยเพิ่มการวิเคราะห์การกระจายตัว (dispersion) ทำให้เข้าใจความผันผวนของข้อมูลได้ดียิ่งขึ้น สำหรับการวิเคราะห์ในโดเมนความถี่ Fourier Transform และ Periodogram Analysis ช่วยในการระบุรอบของฤดูกาลได้อย่างแม่นยำ 

Wavelet Analysis นับเป็นเครื่องมือที่ทรงพลังสำหรับฤดูกาลที่มีหลายความถี่หรือเปลี่ยนแปลงตามเวลา ขณะที่ Seasonal Strength Index เป็นตัวชี้วัดเชิงปริมาณที่ช่วยวัดความเข้มของฤดูกาล ทำให้สามารถเปรียบเทียบและประเมินผลได้อย่างเป็นรูปธรรม

## 3. การบ่งชี้ความคงที่ (Stationarity)

ความคงที่เป็นคุณสมบัติสำคัญที่ต้องพิจารณาก่อนสร้างโมเดล ARIMA

### 3.1 การวิเคราะห์ความคงที่จากกราฟ

ข้อมูลที่มีความคงที่จะแสดงลักษณะดังนี้:
- **ค่าเฉลี่ยคงที่**: ข้อมูลแกว่งรอบค่าคงที่ ไม่มีแนวโน้มเพิ่มขึ้นหรือลดลง
- **ความแปรปรวนคงที่**: ความกว้างของการแกว่งตัวไม่เปลี่ยนแปลงมาก
- **ไม่มีวัฏจักรระยะยาว**: ไม่มีรูปแบบขึ้นลงในระยะยาวที่ไม่เกี่ยวกับฤดูกาล

### 3.2 การดู Simple Moving Average (SMA)

SMA ช่วยในการระบุความคงที่:
- **ข้อมูลคงที่**: SMA จะแกว่งรอบค่าคงที่ ไม่มีแนวโน้มชัดเจน
- **ข้อมูลไม่คงที่**: SMA จะมีแนวโน้มเพิ่มขึ้นหรือลดลงอย่างต่อเนื่อง

### 3.3 การใช้ค่าเฉลี่ยของความต่าง (Mean of Differences)

การคำนวณความต่างระหว่างข้อมูลติดกัน (differencing) และวิเคราะห์ค่าเฉลี่ย:
- **ค่าเฉลี่ยใกล้ศูนย์**: บ่งชี้ว่าข้อมูลหลังทำ differencing มีความคงที่
- **ค่าเฉลี่ยไม่ใกล้ศูนย์**: อาจต้องทำ differencing เพิ่ม

### 3.4 การทดสอบความคงที่ขั้นสูง

เทคนิคสมัยใหม่ในการทดสอบความคงที่:

- **การทดสอบ Augmented Dickey-Fuller (ADF)** - ทดสอบสมมติฐานว่าข้อมูลมี unit root (ไม่คงที่)
- **การทดสอบ Kwiatkowski-Phillips-Schmidt-Shin (KPSS)** - ทดสอบสมมติฐานว่าข้อมูลคงที่
- **การทดสอบ Zivot-Andrews** - ทดสอบความคงที่โดยคำนึงถึง structural breaks
- **การทดสอบ Phillips-Perron (PP)** - ทดสอบคล้าย ADF แต่ไม่มีข้อสมมติเรื่องความเป็นอิสระของความคลาดเคลื่อน
- **Fractional Differencing** - ทางเลือกสำหรับข้อมูลที่มี long memory (รูปแบบการพึ่งพาระยะยาว)

## 4. การวิเคราะห์ ACF และ PACF

### 4.1 Autocorrelation Function (ACF)

ACF แสดงความสัมพันธ์ระหว่างข้อมูล ณ เวลาต่างๆ:
- **ข้อมูลไม่คงที่**: ACF ลดลงอย่างช้าๆ (slow decay)
- **ข้อมูลคงที่**: ACF ลดลงอย่างรวดเร็ว (quick decay) หรือตัดหลังจาก lag ไม่กี่ตัว
- **ข้อมูลมีฤดูกาล**: ACF จะมีค่าสูงที่ lag ซึ่งเป็นความยาวของฤดูกาล (เช่น lag 24 สำหรับข้อมูลรายชั่วโมง)

### 4.2 Partial Autocorrelation Function (PACF)

PACF แสดงความสัมพันธ์โดยตรงระหว่างข้อมูลที่ห่างกัน k ช่วงเวลา:
- **AR(p) process**: PACF จะตัดหลัง lag p
- **MA(q) process**: ACF จะตัดหลัง lag q
- **ARMA(p,q) process**: ทั้ง ACF และ PACF ลดลงแบบหางยาว (tail off)

### 4.3 แนวทางการตีความ ACF และ PACF เพื่อกำหนดพารามิเตอร์

การตีความกราฟ ACF และ PACF เพื่อกำหนดพารามิเตอร์:

| รูปแบบที่สังเกตเห็น | การตีความ | พารามิเตอร์ที่แนะนำ |
|-------------|-------------|-------------|
| ACF: ลดลงแบบหางยาว, PACF: ตัดที่ lag p | AR(p) process | (p,d,0) |
| ACF: ตัดที่ lag q, PACF: ลดลงแบบหางยาว | MA(q) process | (0,d,q) |
| ACF: ลดลงแบบหางยาว, PACF: ลดลงแบบหางยาว | ARMA(p,q) process | (p,d,q) |
| ACF: มีค่าสูงที่ lag เป็นจำนวนเท่าของ s | มีรูปแบบฤดูกาลที่คาบ s | (p,d,q)(P,D,Q)s |

### 4.4 การทดสอบความมีนัยสำคัญ

การทดสอบความมีนัยสำคัญของค่าสหสัมพันธ์:

- **Ljung-Box Test** - ทดสอบว่า autocorrelations โดยรวมเท่ากับศูนย์หรือไม่
- **Box-Pierce Test** - รูปแบบที่ง่ายกว่าของ Ljung-Box
- **Durbin-Watson Test** - ทดสอบความเป็นอิสระของความคลาดเคลื่อน (สำหรับ lag 1)

## 5. การกำหนดพารามิเตอร์ ARIMA(p,d,q)(P,D,Q)s

### 5.1 การกำหนดค่า d (Regular Differencing)

- **d = 0**: เมื่อข้อมูลมีความคงที่อยู่แล้ว ไม่มีแนวโน้ม
- **d = 1**: เมื่อข้อมูลมีแนวโน้มเชิงเส้น (linear trend)
- **d = 2**: เมื่อข้อมูลมีแนวโน้มเป็นพาราโบลา (quadratic trend)

วิธีการตัดสินใจ:
1. ดูกราฟข้อมูลดูว่ามีแนวโน้มหรือไม่
2. ตรวจสอบ SMA ว่ามีแนวโน้มหรือไม่
3. คำนวณค่าเฉลี่ยของความต่าง (หลังทำ differencing) ว่าใกล้ศูนย์หรือไม่
4. ใช้การทดสอบทางสถิติ (เช่น ADF, KPSS)

### 5.2 การกำหนดค่า s (Seasonal Period)

- **s = 24**: สำหรับข้อมูลรายชั่วโมงที่มีฤดูกาลรายวัน
- **s = 168**: สำหรับข้อมูลรายชั่วโมงที่มีฤดูกาลรายสัปดาห์ (24×7)
- **s = 7**: สำหรับข้อมูลรายวันที่มีฤดูกาลรายสัปดาห์
- **s = 12**: สำหรับข้อมูลรายเดือนที่มีฤดูกาลรายปี
- **s = 4**: สำหรับข้อมูลรายไตรมาสที่มีฤดูกาลรายปี

วิธีการตัดสินใจ:
1. ดูกราฟแยกตามช่วงเวลา (เช่น ชั่วโมง, วัน, เดือน)
2. ตรวจสอบ ACF สำหรับค่าสูงที่ lag ที่สอดคล้องกับรอบฤดูกาล
3. ใช้ Periodogram หรือ Fourier Analysis เพื่อระบุความถี่ที่โดดเด่น

### 5.3 การกำหนดค่า D (Seasonal Differencing)

- **D = 0**: เมื่อข้อมูลไม่มีแนวโน้มตามฤดูกาล
- **D = 1**: เมื่อรูปแบบฤดูกาลมีการเปลี่ยนแปลงค่าเฉลี่ยตามเวลา

วิธีการตัดสินใจ:
1. ตรวจสอบว่าความสูงของจุดสูงสุดและต่ำสุดในแต่ละรอบฤดูกาลมีการเปลี่ยนแปลงตามเวลาหรือไม่
2. ใช้การทดสอบทางสถิติเฉพาะสำหรับฤดูกาล (เช่น OCSB, CH test)
3. ทำการทดสอบ seasonal unit root (เช่น HEGY test)

### 5.4 การกำหนดค่า p และ q (Regular Component)

- **p**: จำนวนค่า AR terms
- **q**: จำนวนค่า MA terms

วิธีการตัดสินใจ:
1. ตรวจสอบ ACF และ PACF หลังจากทำ differencing แล้ว
2. ถ้า ACF ตัดที่ lag q และ PACF ลดลงแบบหางยาว → MA(q)
3. ถ้า PACF ตัดที่ lag p และ ACF ลดลงแบบหางยาว → AR(p)
4. ถ้าทั้ง ACF และ PACF ลดลงแบบหางยาว → ARMA(p,q)

### 5.5 การกำหนดค่า P และ Q (Seasonal Component)

- **P**: จำนวนค่า seasonal AR terms
- **Q**: จำนวนค่า seasonal MA terms

วิธีการตัดสินใจ:
1. ตรวจสอบ ACF และ PACF ที่ lag เป็นจำนวนเท่าของ s
2. ใช้หลักการเดียวกับการกำหนดค่า p และ q โดยมุ่งเน้นที่ lag s, 2s, 3s, ...

### 5.6 เทคนิคการเลือกโมเดลอัตโนมัติ (2024)

เทคนิคสมัยใหม่ในการเลือกโมเดล:

- **Auto-ARIMA** - อัลกอริทึมที่ทดลองค่าพารามิเตอร์ต่างๆ และเลือกโมเดลที่ดีที่สุดตามเกณฑ์ข้อมูล (เช่น pmdarima ใน Python, auto.arima ใน R)
- **Grid Search** - ค้นหาเชิงระบบผ่านพารามิเตอร์ต่างๆ และเลือกโมเดลที่ดีที่สุดตามเกณฑ์ (เช่น AIC, BIC)
- **Bayesian Optimization** - วิธีการขั้นสูงในการค้นหาพารามิเตอร์ที่เหมาะสมที่สุด โดยใช้แนวคิดของการเรียนรู้เชิงเบย์
- **Genetic Algorithms** - ใช้แนวคิดวิวัฒนาการในการค้นหาพารามิเตอร์ที่เหมาะสม

## 6. แนวทางการวิเคราะห์แบบบูรณาการ

### 6.1 ขั้นตอนการวิเคราะห์ที่แนะนำ

1. **ดูกราฟในหลายมาตราส่วนเวลา** เพื่อเข้าใจภาพรวมและรายละเอียด
2. **วิเคราะห์รูปแบบฤดูกาล** และระบุความยาวของฤดูกาล (s)
3. **ตรวจสอบความคงที่** และพิจารณาความจำเป็นในการทำ differencing (d, D)
4. **วิเคราะห์ ACF และ PACF** เพื่อกำหนดค่า p, q, P, Q
5. **ตัดสินใจแบบบูรณาการ** โดยใช้หลายวิธีร่วมกัน และพิจารณาทั้งลักษณะกราฟและผลการทดสอบทางสถิติ
6. **สร้างและประเมินโมเดล** โดยวิเคราะห์ความเหมาะสมของโมเดลและความแม่นยำในการพยากรณ์

### 6.2 ข้อควรระวัง

- **ระวัง Overfitting**: การกำหนดค่าพารามิเตอร์มากเกินไปอาจทำให้โมเดลไม่สามารถทำนายข้อมูลใหม่ได้ดี
- **เลือกใช้ค่า d และ D น้อยที่สุดเท่าที่จำเป็น**: การทำ differencing มากเกินไปอาจทำให้เกิดปัญหาในโมเดล
- **พิจารณาโมเดลทางเลือก**: บางกรณีอาจต้องใช้โมเดลที่ซับซ้อนกว่า SARIMA เช่น โมเดลที่มีฤดูกาลหลายระดับ หรือโมเดลที่รวมตัวแปรภายนอก

### 6.3 เทคนิคการตรวจสอบความเหมาะสมของโมเดล

การตรวจสอบความเหมาะสมของโมเดลหลังจากสร้างแล้ว:

- **การวิเคราะห์ค่าคงเหลือ (Residual Analysis)** - ตรวจสอบว่าค่าคงเหลือเป็นสัญญาณรบกวนสีขาว (white noise) หรือไม่
- **การทดสอบ Ljung-Box** - ทดสอบว่าค่าคงเหลือมี autocorrelation หรือไม่
- **QQ Plot** - ตรวจสอบการแจกแจงปกติของค่าคงเหลือ
- **การตรวจสอบ heteroscedasticity** - ตรวจสอบว่าความแปรปรวนของค่าคงเหลือคงที่หรือไม่

## 7. กรณีศึกษา: การวิเคราะห์ข้อมูลการผลิตไฟฟ้า

เมื่อวิเคราะห์ข้อมูลการผลิตไฟฟ้าที่มีรูปแบบฤดูกาลชัดเจนทั้ง 3 ระดับ:

### 7.1 การพิจารณากราฟค่าเฉลี่ยตามชั่วโมง
- รูปแบบรายวันชัดเจน: สูงในช่วงกลางวัน (ชั่วโมงที่ 5-10) และต่ำในช่วงกลางคืน (ชั่วโมงที่ 18-23)
- บ่งชี้ว่าควรใช้ s = 24 สำหรับฤดูกาลรายวัน

### 7.2 การพิจารณากราฟค่าเฉลี่ยตามวันในสัปดาห์
- รูปแบบรายสัปดาห์ชัดเจน: สูงในวันทำงาน (จันทร์-ศุกร์) และต่ำในวันหยุด (เสาร์-อาทิตย์)
- บ่งชี้ว่าอาจต้องใช้ s = 7 หรือ s = 168 (7×24) สำหรับฤดูกาลรายสัปดาห์
- หรืออาจเพิ่มตัวแปรหุ่นสำหรับวันหยุดสุดสัปดาห์ใน SARIMAX

### 7.3 การพิจารณากราฟค่าเฉลี่ยตามเดือน
- รูปแบบรายปีชัดเจน: สูงในช่วงฤดูร้อน (มีนาคม-พฤษภาคม) และฤดูหนาว (พฤศจิกายน-ธันวาคม) และต่ำในช่วงฤดูฝน (กรกฎาคม-กันยายน)
- บ่งชี้ว่าควรคำนึงถึงฤดูกาลรายปีในโมเดล

### 7.4 การตัดสินใจเลือกโมเดล
จากรูปแบบที่ชัดเจนทั้ง 3 ระดับ อาจพิจารณาโมเดลต่อไปนี้:

1. **SARIMA** ที่มีพารามิเตอร์ฤดูกาลเหมาะสม
   - SARIMA(p,1,q)(P,1,Q)24 สำหรับฤดูกาลรายวัน
   - อาจเพิ่มตัวแปรหุ่นสำหรับวันหยุดสุดสัปดาห์และวันหยุดพิเศษ

2. **โมเดลที่รองรับฤดูกาลหลายระดับ**
   - TBATS (Trigonometric seasonality, Box-Cox transformation, ARMA errors, Trend, and Seasonal components)
   - Prophet โดย Facebook (รองรับฤดูกาลหลายระดับและจุดเปลี่ยน)
   - NeuralProphet (รวมคุณสมบัติของ Prophet และ Neural Networks)

3. **โมเดลลักษณะผสม (Hybrid Models)**
   - ผสมระหว่างโมเดล SARIMA กับ Neural Networks เพื่อจับรูปแบบเชิงเส้นและไม่เชิงเส้น
   - SARIMAX ที่เพิ่มตัวแปรภายนอก เช่น อุณหภูมิ วันหยุด กิจกรรมทางเศรษฐกิจ

## 8. โมเดลอนุกรมเวลาสมัยใหม่ (2024)

นอกจากโมเดล ARIMA และ SARIMA แล้ว ปัจจุบันมีโมเดลอนุกรมเวลาสมัยใหม่ที่น่าสนใจ:

### 8.1 โมเดลแยกองค์ประกอบ (Decomposition Models)

- **STL, MSTL, OneShotSTL** - การแยกองค์ประกอบแนวโน้มและฤดูกาลโดยใช้ LOESS
- **STD (Seasonal-Trend-Dispersion)** - ขยาย STL ด้วยการวิเคราะห์การกระจายตัว
- **RobustSTL** - เวอร์ชันที่ทนทานต่อค่าผิดปกติ

### 8.2 โมเดลฤดูกาลแบบยืดหยุ่น (Flexible Seasonal Models)

- **TBATS** - รองรับฤดูกาลหลายระดับและฤดูกาลที่ไม่เป็นจำนวนเต็ม
- **BATS** - เหมือน TBATS แต่ไม่รวมส่วนของตรีโกณมิติ
- **Prophet** - รองรับฤดูกาลหลายระดับ, จุดเปลี่ยน, และวันหยุด

### 8.3 โมเดลเรียนรู้เชิงลึก (Deep Learning Models)

- **LSTM (Long Short-Term Memory)** - โครงข่ายประสาทเทียมที่เหมาะสำหรับอนุกรมเวลา
- **TCN (Temporal Convolutional Networks)** - โครงข่ายประสาทเทียมแบบคอนโวลูชันสำหรับอนุกรมเวลา
- **N-BEATS** - โครงข่ายประสาทเทียมที่ออกแบบเฉพาะสำหรับการพยากรณ์อนุกรมเวลา
- **Transformer-based Models** - โมเดลที่ใช้กลไก self-attention เช่น TFT (Temporal Fusion Transformer), TimeGPT

### 8.4 โมเดลผสม (Hybrid Models)

- **SARIMA + LSTM** - ผสมระหว่างโมเดลเชิงเส้นและไม่เชิงเส้น
- **Prophet + XGBoost** - ผสมระหว่างโมเดลแยกองค์ประกอบกับการเรียนรู้ของเครื่อง
- **Ensemble Models** - รวมผลการพยากรณ์จากหลายโมเดล

### 8.5 เกณฑ์การเลือกโมเดล

การเลือกระหว่างโมเดลดั้งเดิม (ARIMA/SARIMA) กับโมเดลสมัยใหม่:

- **ใช้ ARIMA/SARIMA เมื่อ**:
  - ข้อมูลมีรูปแบบเชิงเส้นชัดเจน
  - ต้องการโมเดลที่อธิบายได้และตีความง่าย
  - มีข้อมูลไม่มาก (น้อยกว่า 1,000 จุด)
  
- **ใช้โมเดลสมัยใหม่เมื่อ**:
  - ข้อมูลมีรูปแบบไม่เชิงเส้นหรือซับซ้อนมาก
  - มีฤดูกาลหลายระดับหรือฤดูกาลที่เปลี่ยนแปลง
  - มีข้อมูลจำนวนมาก (มากกว่า 1,000 จุด)
  - ต้องการความแม่นยำสูงสุดโดยไม่คำนึงถึงการตีความ

## 9. แนวทางการประเมินและตรวจสอบโมเดลอนุกรมเวลา

### 9.1 การแบ่งข้อมูลสำหรับอนุกรมเวลา

การแบ่งข้อมูลที่เหมาะสมสำหรับอนุกรมเวลา:

- **Time Series Split** - แบ่งข้อมูลโดยเวลา ข้อมูลทดสอบต้องอยู่หลังข้อมูลฝึกฝนเสมอ
- **Rolling Window Validation** - ฝึกฝนโมเดลบนหน้าต่างเคลื่อนที่ และทดสอบกับข้อมูลถัดไป
- **Expanding Window Validation** - เพิ่มขนาดของข้อมูลฝึกฝนในแต่ละรอบ

### 9.2 เกณฑ์การประเมินประสิทธิภาพ

เกณฑ์สำหรับประเมินประสิทธิภาพของโมเดลพยากรณ์:

- **MAE (Mean Absolute Error)** - วัดค่าเฉลี่ยของความคลาดเคลื่อนสัมบูรณ์
- **RMSE (Root Mean Squared Error)** - วัดค่ารากที่สองของค่าเฉลี่ยความคลาดเคลื่อนกำลังสอง
- **MAPE (Mean Absolute Percentage Error)** - วัดค่าเฉลี่ยของความคลาดเคลื่อนเป็นเปอร์เซ็นต์
- **SMAPE (Symmetric Mean Absolute Percentage Error)** - เวอร์ชันสมมาตรของ MAPE
- **MASE (Mean Absolute Scaled Error)** - เปรียบเทียบกับโมเดลอ้างอิงแบบง่าย
- **CRPS (Continuous Ranked Probability Score)** - สำหรับการพยากรณ์แบบความน่าจะเป็น

### 9.3 การเลือกเกณฑ์การประเมินที่เหมาะสม

แนวทางการเลือกเกณฑ์การประเมิน:

- **ใช้ MAE หรือ RMSE เมื่อ**: ข้อมูลมีหน่วยที่มีความหมาย และทุกข้อมูลมีความสำคัญเท่ากัน
- **ใช้ MAPE หรือ SMAPE เมื่อ**: ต้องการเปรียบเทียบประสิทธิภาพระหว่างชุดข้อมูลที่มีขนาดต่างกัน
- **ใช้ MASE เมื่อ**: ข้อมูลมีค่าศูนย์หรือค่าใกล้ศูนย์ ซึ่งทำให้ MAPE มีปัญหา
- **ใช้ CRPS เมื่อ**: โมเดลให้ผลลัพธ์เป็นการแจกแจงความน่าจะเป็น

## บทสรุป

การวิเคราะห์กราฟอย่างละเอียดช่วยให้สามารถระบุรูปแบบฤดูกาลและความคงที่ของข้อมูล ซึ่งเป็นขั้นตอนสำคัญในการเลือกพารามิเตอร์ที่เหมาะสมสำหรับโมเดล ARIMA/SARIMA การพิจารณาข้อมูลในหลายมาตราส่วนเวลา และการใช้หลายเทคนิคร่วมกัน จะช่วยให้การตัดสินใจมีความแม่นยำมากขึ้น และนำไปสู่โมเดลที่มีประสิทธิภาพในการพยากรณ์

ในปัจจุบัน (2024) นักวิเคราะห์มีทางเลือกมากมาย ตั้งแต่โมเดลดั้งเดิมอย่าง ARIMA/SARIMA ไปจนถึงโมเดลสมัยใหม่อย่าง Deep Learning และโมเดลผสม การเลือกโมเดลที่เหมาะสมขึ้นอยู่กับลักษณะของข้อมูล, ความซับซ้อนของรูปแบบฤดูกาล, ปริมาณข้อมูลที่มี, และวัตถุประสงค์ของการวิเคราะห์ อย่างไรก็ตาม ไม่ว่าจะเลือกใช้โมเดลใด การวิเคราะห์กราฟยังคงเป็นขั้นตอนสำคัญในการทำความเข้าใจข้อมูลและกำหนดพารามิเตอร์ที่เหมาะสม

---