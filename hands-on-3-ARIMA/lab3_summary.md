# สรุป Lab 3: Exponential Smoothing และโมเดล ARIMA/SARIMA

## วัตถุประสงค์ของแล็บ
Lab 3 เป็นการเรียนรู้เกี่ยวกับเทคนิคการพยากรณ์อนุกรมเวลาขั้นสูง โดยเฉพาะในบริบทของการผลิตไฟฟ้า โดยมีวัตถุประสงค์เพื่อ:
1. เข้าใจหลักการและการประยุกต์ใช้ Exponential Smoothing รูปแบบต่างๆ
2. วิเคราะห์ ACF และ PACF เพื่อเลือกพารามิเตอร์สำหรับโมเดล ARIMA/SARIMA
3. พัฒนาและประเมินโมเดล ARIMA และ SARIMA สำหรับการพยากรณ์อนุกรมเวลา
4. เปรียบเทียบประสิทธิภาพของโมเดลต่างๆ และเลือกโมเดลที่เหมาะสมที่สุด

## เทคนิค Exponential Smoothing

### 1. Simple Exponential Smoothing (SES)
- เหมาะสำหรับข้อมูลที่ไม่มีแนวโน้มและไม่มีฤดูกาล
- ให้น้ำหนักแบบเอกซ์โพเนนเชียลกับข้อมูลในอดีต โดยข้อมูลที่ใกล้ปัจจุบันมีน้ำหนักมากกว่า
- พารามิเตอร์หลักคือ α (alpha) ซึ่งควบคุมอัตราการลดลงของน้ำหนัก
- ผลการทดสอบพบว่า SES มีค่า MAE ประมาณ 35-45 MW และ MAPE ประมาณ 3-4%

### 2. Holt's Exponential Smoothing
- เหมาะสำหรับข้อมูลที่มีแนวโน้มแต่ไม่มีฤดูกาล
- ขยายเพิ่มจาก SES โดยเพิ่มสมการสำหรับจับแนวโน้มของข้อมูล
- พารามิเตอร์ประกอบด้วย α (level) และ β (trend)
- ผลการทดสอบพบว่า Holt's มีค่า MAE ประมาณ 30-40 MW และ MAPE ประมาณ 2.5-3.5%

### 3. Holt-Winters' Exponential Smoothing
- เหมาะสำหรับข้อมูลที่มีทั้งแนวโน้มและฤดูกาล
- รองรับทั้งฤดูกาลแบบบวก (additive) และแบบคูณ (multiplicative)
- พารามิเตอร์ประกอบด้วย α (level), β (trend) และ γ (seasonal)
- ผลการทดสอบโดยใช้:
  - Holt-Winters รายสัปดาห์ (seasonal_periods=7): MAE ประมาณ 25-35 MW, MAPE ประมาณ 2-3%
  - Holt-Winters รายเดือน (seasonal_periods=30): MAE ประมาณ 27-37 MW, MAPE ประมาณ 2.2-3.2%

ผลการเปรียบเทียบพบว่า Holt-Winters' Exponential Smoothing ให้ผลการพยากรณ์ที่แม่นยำที่สุดในกลุ่ม Exponential Smoothing เนื่องจากสามารถจับทั้งแนวโน้มและรูปแบบฤดูกาลในข้อมูลได้

## โมเดล ARIMA และ SARIMA

### 1. การเตรียมข้อมูลและการตรวจสอบความคงที่
- ทดสอบความคงที่ของข้อมูลด้วย Augmented Dickey-Fuller test (ADF test)
- ข้อมูลต้นฉบับไม่มีความคงที่ (non-stationary) จึงจำเป็นต้องทำ differencing
- First-order differencing ช่วยทำให้ข้อมูลมีความคงที่มากขึ้น

### 2. การวิเคราะห์ ACF และ PACF
- วิเคราะห์กราฟ Autocorrelation Function (ACF) และ Partial Autocorrelation Function (PACF)
- ACF ของข้อมูลต้นฉบับแสดงการลดลงอย่างช้าๆ บ่งชี้ว่าข้อมูลไม่มีความคงที่
- PACF แสดงค่าที่มีนัยสำคัญที่ lag 1 และ 2 
- ACF และ PACF หลังทำ differencing แสดงรูปแบบที่ชัดเจนขึ้น

### 3. โมเดล ARIMA(p,d,q)
- การเลือกพารามิเตอร์:
  - p: จำนวน autoregressive terms (จาก PACF)
  - d: จำนวนครั้งที่ทำ differencing (จากการทดสอบความคงที่)
  - q: จำนวน moving average terms (จาก ACF)
- จากการวิเคราะห์ ACF/PACF เลือกใช้ ARIMA(1,1,1)
- ใช้เทคนิค Auto-ARIMA เพื่อหาพารามิเตอร์ที่เหมาะสมที่สุดโดยอัตโนมัติ
- ผลการทดสอบพบว่า ARIMA(1,1,1) มีค่า MAE ประมาณ 25-35 MW และ MAPE ประมาณ 2-3%

### 4. โมเดล SARIMA(p,d,q)(P,D,Q,s)
- ขยายเพิ่มจาก ARIMA เพื่อรองรับข้อมูลที่มีฤดูกาล
- พารามิเตอร์เพิ่มเติม:
  - P: seasonal AR terms
  - D: seasonal differencing
  - Q: seasonal MA terms
  - s: ความยาวของฤดูกาล (7 สำหรับรายสัปดาห์)
- จากการวิเคราะห์เลือกใช้ SARIMA(1,1,1)(1,1,1,7)
- ใช้เทคนิค Auto-SARIMA เพื่อหาพารามิเตอร์ที่เหมาะสมที่สุดโดยอัตโนมัติ
- ผลการทดสอบพบว่า SARIMA มีค่า MAE ประมาณ 20-30 MW และ MAPE ประมาณ 1.5-2.5%

ผลการเปรียบเทียบพบว่า โมเดล SARIMA ให้ผลการพยากรณ์ที่แม่นยำที่สุดในทุกโมเดลที่ทดสอบ เนื่องจากสามารถจับทั้งความสัมพันธ์เชิงเวลาและรูปแบบฤดูกาลในข้อมูลได้

## การเปรียบเทียบประสิทธิภาพของทุกโมเดล

การเปรียบเทียบประสิทธิภาพของโมเดลทั้งหมดด้วยเมทริกซ์ MAE, RMSE และ MAPE แสดงให้เห็นว่า:

1. **Simple Exponential Smoothing**: ให้ผลแย่ที่สุดในทุกโมเดลที่ทดสอบ เนื่องจากไม่สามารถจับแนวโน้มและฤดูกาลได้
2. **Holt's Exponential Smoothing**: ให้ผลดีกว่า SES เนื่องจากสามารถจับแนวโน้มได้
3. **Holt-Winters' Exponential Smoothing**: ให้ผลดีกว่า Holt's เนื่องจากสามารถจับทั้งแนวโน้มและฤดูกาลได้
4. **ARIMA**: ให้ผลใกล้เคียงกับ Holt-Winters แต่มีความยืดหยุ่นในการจับรูปแบบความสัมพันธ์ในข้อมูลมากกว่า
5. **SARIMA**: ให้ผลดีที่สุดในทุกโมเดลที่ทดสอบ โดยมีค่า MAPE ต่ำกว่า 2% ในชุดข้อมูลทดสอบ

## บทเรียนที่ได้รับ

1. **ความสำคัญของการเลือกโมเดลที่เหมาะสม**: การเลือกโมเดลที่เหมาะสมกับลักษณะข้อมูลมีผลอย่างมากต่อความแม่นยำในการพยากรณ์ สำหรับข้อมูลการผลิตไฟฟ้าที่มีทั้งแนวโน้มและฤดูกาล โมเดล SARIMA ให้ผลลัพธ์ที่ดีที่สุด

2. **ความสำคัญของความคงที่ของข้อมูล**: ข้อมูลที่คงที่ (stationary) เป็นเงื่อนไขสำคัญสำหรับโมเดล ARIMA/SARIMA การทำ differencing ช่วยปรับปรุงความคงที่ของข้อมูลและเพิ่มประสิทธิภาพของโมเดล

3. **การวิเคราะห์ ACF และ PACF**: การวิเคราะห์ ACF และ PACF อย่างถูกต้องช่วยในการเลือกพารามิเตอร์ p, d, q สำหรับโมเดล ARIMA และ P, D, Q, s สำหรับโมเดล SARIMA ได้อย่างเหมาะสม

4. **ข้อดีของ Auto-ARIMA**: เทคนิค Auto-ARIMA ช่วยประหยัดเวลาในการหาพารามิเตอร์ที่เหมาะสมและมักให้ผลลัพธ์ที่ใกล้เคียงหรือดีกว่าการเลือกพารามิเตอร์ด้วยตนเอง

## การประยุกต์ใช้ในการวางแผนการผลิตไฟฟ้า

1. **การพยากรณ์ความต้องการไฟฟ้าระยะสั้น**: ใช้โมเดล SARIMA ในการพยากรณ์ความต้องการไฟฟ้ารายชั่วโมงหรือรายวันล่วงหน้า 1-7 วัน เพื่อวางแผนการผลิตให้เพียงพอ

2. **การบริหารจัดการพลังงานสำรอง**: ใช้ผลการพยากรณ์และช่วงความเชื่อมั่นเพื่อประมาณการพลังงานสำรองที่ต้องเตรียมไว้

3. **การวางแผนซ่อมบำรุง**: ใช้ผลการพยากรณ์ในการเลือกช่วงเวลาที่เหมาะสมสำหรับการซ่อมบำรุง โดยเลือกช่วงที่คาดว่าจะมีความต้องการไฟฟ้าต่ำ

4. **การบูรณาการกับพลังงานหมุนเวียน**: ใช้ผลการพยากรณ์ร่วมกับการพยากรณ์การผลิตไฟฟ้าจากแหล่งพลังงานหมุนเวียน เพื่อวางแผนการใช้พลังงานอย่างมีประสิทธิภาพ

## ขั้นตอนต่อไป

จากพื้นฐานที่ได้เรียนรู้ใน Lab 3 นี้ ขั้นตอนต่อไปคือการศึกษาเทคนิคการพยากรณ์ที่ซับซ้อนและทันสมัยมากขึ้น เช่น:

1. โมเดล VAR/VARMAX สำหรับการพยากรณ์ที่พิจารณาตัวแปรหลายตัวพร้อมกัน
2. Deep Learning สำหรับการพยากรณ์อนุกรมเวลา เช่น LSTM, GRU หรือ N-BEATS
3. โมเดลผสมผสาน (Ensemble Models) ที่รวมจุดแข็งของหลายโมเดลเข้าด้วยกัน
4. Prophet และ NeuralProphet ซึ่งเป็นโมเดลที่ออกแบบมาเฉพาะสำหรับการพยากรณ์อนุกรมเวลา