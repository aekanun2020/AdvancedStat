# ต้นไม้การตัดสินใจสำหรับการพัฒนาโมเดล ARIMA และ SARIMA

## 1. การพัฒนาโมเดล Time Series ด้วย Auto-ARIMA

```
การพัฒนาโมเดล Time Series ด้วย Auto-ARIMA
├── 1. การเตรียมและวิเคราะห์ข้อมูลเบื้องต้น
│   ├── ตรวจหา pattern หลัก
│   │   ├── แนวโน้ม (Trend)
│   │   ├── ฤดูกาล (Seasonality)
│   │   │   ├── รายชั่วโมง (s=24)
│   │   │   ├── รายวัน (s=7)
│   │   │   ├── รายสัปดาห์ (s=52)
│   │   │   └── รายปี (s=12 สำหรับรายเดือน)
│   │   ├── วัฏจักร (Cyclical)
│   │   └── ความไม่สม่ำเสมอ (Irregular)
│   ├── การตรวจสอบคุณภาพข้อมูล
│   │   ├── ตรวจสอบและจัดการข้อมูลที่หายไป (missing values)
│   │   ├── ตรวจจับและจัดการค่าผิดปกติ (outliers)
│   │   └── ตรวจสอบการเปลี่ยนแปลงโครงสร้าง (structural changes)
│   └── การแปลงข้อมูล (transformations)
│       ├── ไม่แปลง (ข้อมูลมีคุณสมบัติที่ดีอยู่แล้ว)
│       ├── Box-Cox transformation
│       │   ├── การเลือกค่า lambda อัตโนมัติ (Guerrero's method)
│       │   ├── การเลือกค่า lambda ด้วย Maximum Likelihood
│       │   └── การใช้ค่า lambda พิเศษ (0 = log, 0.5 = sqrt, 1 = no transform)
│       ├── Log transformation (สำหรับข้อมูลที่มีความแปรปรวนตามระดับ)
│       └── Seasonal adjustment (ปรับฤดูกาลก่อนการวิเคราะห์)
│
├── 2. การกำหนดค่า d และ D (differencing order)
│   ├── การหาค่า d ที่เหมาะสม
│   │   ├── การทดสอบความคงที่ (Stationarity)
│   │   │   ├── KPSS Test (null: series is stationary)
│   │   │   ├── ADF Test (null: series has unit root/non-stationary)
│   │   │   └── PP Test (robust to heteroscedasticity)
│   │   ├── การตรวจสอบผลลัพธ์ที่ขัดแย้งกัน
│   │   │   ├── กรณี KPSS และ ADF ให้ผลขัดแย้ง (ทั่วไปในข้อมูลจริง)
│   │   │   └── ควรเลือกตามสมบัติข้อมูลและเป้าหมายการพยากรณ์
│   │   └── การตัดสินใจระดับ differencing
│   │       ├── d = 0 (ข้อมูลคงที่แล้ว)
│   │       ├── d = 1 (ส่วนใหญ่เพียงพอสำหรับข้อมูลเศรษฐกิจและธรรมชาติ)
│   │       └── d = 2 (สำหรับข้อมูลที่มีแนวโน้มเร่ง/ชะลอตัว)
│   └── การหาค่า D ที่เหมาะสม (seasonal differencing)
│       ├── การทดสอบ seasonal unit roots
│       │   ├── Canova-Hansen test (null: deterministic seasonality)
│       │   └── OCSB test (Osborn-Chui-Smith-Birchenhall test)
│       ├── การทดสอบ seasonal stationarity
│       │   ├── Seasonal KPSS test
│       │   └── Hylleberg-Engle-Granger-Yoo (HEGY) test
│       └── การตัดสินใจระดับ seasonal differencing
│           ├── D = 0 (ไม่จำเป็นต้องทำ seasonal differencing)
│           └── D = 1 (ต้องทำ seasonal differencing 1 ครั้ง)
│
├── 3. วิธีการค้นหาค่า p, q, P, Q ที่เหมาะสม
│   ├── การค้นหาแบบ Manual
│   │   ├── วิเคราะห์ ACF และ PACF หลัง differencing
│   │   │   ├── ACF: ค่าที่มีนัยสำคัญและรูปแบบการลดลง
│   │   │   │   ├── ตัดที่ lag q → MA(q)
│   │   │   │   └── ลดลงแบบหางยาว → AR component
│   │   │   ├── PACF: ค่าที่มีนัยสำคัญและรูปแบบการลดลง
│   │   │   │   ├── ตัดที่ lag p → AR(p)
│   │   │   │   └── ลดลงแบบหางยาว → MA component
│   │   │   └── การแปลผลรูปแบบพิเศษ
│   │   │       ├── ทั้ง ACF และ PACF ลดลงแบบ exponential → ARMA
│   │   │       └── มีรูปแบบคลื่นไซน์ → seasonal component
│   │   └── การค้นหาค่า P และ Q
│   │       ├── ตรวจสอบ ACF และ PACF ที่ lag ฤดูกาล (s, 2s, 3s, ...)
│   │       ├── ใช้หลักการเดียวกับการหา p และ q
│   │       └── เน้นการพิจารณาเฉพาะ lag ที่เป็นจำนวนเท่าของ s
│   │
│   └── การใช้ Auto-ARIMA
│       ├── วิธีการค้นหาอัตโนมัติ
│       │   ├── Stepwise search 
│       │   │   ├── เริ่มจากโมเดลพื้นฐาน แล้วปรับพารามิเตอร์ทีละขั้น
│       │   │   ├── เร็วกว่า แต่อาจติดอยู่ใน local minimum
│       │   │   └── เป็นค่าเริ่มต้นใน pmdarima และ forecast package
│       │   └── Exhaustive grid search
│       │       ├── ทดสอบทุกความเป็นไปได้ภายในขอบเขตที่กำหนด
│       │       ├── ใช้เวลามากกว่า แต่มีโอกาสเจอ global minimum
│       │       └── เหมาะกับชุดข้อมูลขนาดเล็กถึงกลาง
│       ├── การกำหนดขอบเขตการค้นหา
│       │   ├── start_p, max_p (กำหนดค่า p ขั้นต่ำและสูงสุด)
│       │   ├── start_q, max_q (กำหนดค่า q ขั้นต่ำและสูงสุด)
│       │   ├── max_d (กำหนดค่า d สูงสุด หากไม่ได้กำหนดค่าตายตัว)
│       │   ├── seasonal (เปิด/ปิดการค้นหาองค์ประกอบฤดูกาล)
│       │   ├── m (กำหนดความยาวของฤดูกาล เช่น 12 สำหรับรายเดือน)
│       │   └── start_P, max_P, start_Q, max_Q (กำหนดขอบเขตของพารามิเตอร์ฤดูกาล)
│       ├── เกณฑ์การเลือกโมเดล
│       │   ├── AIC (Akaike Information Criterion)
│       │   │   ├── เน้นความสมดุลระหว่างความซับซ้อนและความแม่นยำ
│       │   │   └── AIC = 2k - 2ln(L) โดย k = จำนวนพารามิเตอร์, L = likelihood
│       │   ├── AICc (Corrected AIC)
│       │   │   ├── เหมาะกับชุดข้อมูลขนาดเล็ก
│       │   │   └── มีการปรับแก้เพื่อป้องกัน overfitting ในตัวอย่างขนาดเล็ก
│       │   ├── BIC (Bayesian Information Criterion)
│       │   │   ├── มีบทลงโทษความซับซ้อนมากกว่า AIC
│       │   │   └── มักได้โมเดลที่เรียบง่ายกว่า
│       │   └── HQIC (Hannan-Quinn Information Criterion)
│       │       ├── ทางเลือกระหว่าง AIC และ BIC
│       │       └── มีคุณสมบัติทางทฤษฎีที่ดีสำหรับขนาดตัวอย่างใหญ่
│       └── การทำงานขนานและการเพิ่มประสิทธิภาพ
│           ├── n_jobs (กำหนดจำนวน parallel processes)
│           ├── approximation (ใช้การประมาณการเพื่อเร่งความเร็ว)
│           └── การใช้ GPU (สำหรับชุดข้อมูลขนาดใหญ่มาก)
│
├── 4. การประเมินและปรับแต่งโมเดล
│   ├── การวิเคราะห์ residuals
│   │   ├── การตรวจสอบ white noise
│   │   │   ├── ACF และ PACF ของ residuals (ไม่ควรมีค่ามีนัยสำคัญ)
│   │   │   ├── Ljung-Box test (H₀: residuals เป็น white noise)
│   │   │   └── Box-Pierce test (คล้าย Ljung-Box แต่สำหรับตัวอย่างขนาดใหญ่)
│   │   ├── การตรวจสอบการกระจายแบบปกติ
│   │   │   ├── Q-Q plot ของ residuals
│   │   │   ├── Shapiro-Wilk test (สำหรับตัวอย่างขนาดเล็ก)
│   │   │   └── Jarque-Bera test (ตรวจสอบความเบ้และความโด่ง)
│   │   └── การตรวจสอบ homoscedasticity
│   │       ├── Plot residuals vs fitted values
│   │       ├── Breusch-Pagan test
│   │       └── White test
│   ├── การปรับแต่งโมเดลหากจำเป็น
│   │   ├── การจัดการค่าผิดปกติ (outliers)
│   │   │   ├── การใช้ dummy variables
│   │   │   ├── Intervention analysis
│   │   │   └── Robust estimation methods
│   │   ├── การแปลงข้อมูลเพิ่มเติม
│   │   │   ├── ปรับ Box-Cox lambda
│   │   │   └── ลองการแปลงแบบอื่น (เช่น log, sqrt)
│   │   └── การปรับโมเดล
│   │       ├── ปรับเพิ่ม/ลดค่า p, d, q, P, D, Q
│   │       ├── ปรับเปลี่ยนวิธีการประมาณค่าพารามิเตอร์
│   │       └── ทดลองโมเดลทางเลือก (เช่น ARIMAX, GARCH)
│   └── การตรวจสอบเสถียรภาพของโมเดล
│       ├── การทดสอบบน subsets ของข้อมูล
│       ├── Rolling window analysis
│       └── Sensitivity analysis (การปรับเปลี่ยนพารามิเตอร์เล็กน้อย)
│
├── 5. การประเมินความแม่นยำของการพยากรณ์
│   ├── เทคนิคการแบ่งข้อมูล
│   │   ├── Train-test chronological split
│   │   │   ├── แบ่งตามลำดับเวลา (ไม่สลับข้อมูล)
│   │   │   └── ตัวอย่าง: 80% แรกเป็น training, 20% หลังเป็น testing
│   │   ├── Time series cross-validation
│   │   │   ├── Expanding window
│   │   │   │   ├── เริ่มจากกลุ่มข้อมูลเล็กๆ และค่อยๆ เพิ่มข้อมูล
│   │   │   │   └── พยากรณ์ช่วงเวลาถัดไปและประเมินความแม่นยำ
│   │   │   └── Rolling window
│   │   │       ├── ใช้หน้าต่างข้อมูลขนาดคงที่ที่เลื่อนไปตามเวลา
│   │   │       └── เหมาะกับข้อมูลที่มีการเปลี่ยนแปลงโครงสร้าง
│   │   └── Multiple train-test splits
│   │       ├── แบ่งข้อมูลเป็นหลายช่วงเวลา
│   │       └── ประเมินความแม่นยำในแต่ละช่วง
│   ├── เมทริกซ์การประเมินผล
│   │   ├── Scale-dependent metrics
│   │   │   ├── MAE (Mean Absolute Error)
│   │   │   │   ├── ง่ายต่อการตีความ
│   │   │   │   └── MAE = mean(|y_true - y_pred|)
│   │   │   ├── RMSE (Root Mean Squared Error)
│   │   │   │   ├── ให้น้ำหนักกับความผิดพลาดขนาดใหญ่
│   │   │   │   └── RMSE = sqrt(mean((y_true - y_pred)²))
│   │   │   └── MSE (Mean Squared Error)
│   │   │       ├── มักใช้ในการฝึกโมเดล
│   │   │       └── MSE = mean((y_true - y_pred)²)
│   │   ├── Percentage-based metrics
│   │   │   ├── MAPE (Mean Absolute Percentage Error)
│   │   │   │   ├── แสดงเป็นเปอร์เซ็นต์ความผิดพลาด
│   │   │   │   └── MAPE = mean(|100 * (y_true - y_pred) / y_true|)
│   │   │   └── sMAPE (Symmetric MAPE)
│   │   │       ├── แก้ปัญหาการหารด้วยศูนย์
│   │   │       └── sMAPE = mean(200 * |y_true - y_pred| / (|y_true| + |y_pred|))
│   │   └── Scale-free metrics
│   │       ├── MASE (Mean Absolute Scaled Error)
│   │       │   ├── เปรียบเทียบกับวิธี naive forecast
│   │       │   └── ทนต่อค่าศูนย์หรือค่าใกล้ศูนย์
│   │       └── Theil's U statistic
│   │           ├── เปรียบเทียบกับ random walk forecast
│   │           └── U < 1 หมายถึงโมเดลดีกว่า random walk
│   └── การประเมินเชิงคุณภาพ
│       ├── การตรวจสอบวิธีจับจังหวะในการเปลี่ยนแปลง
│       ├── การพิจารณาความสมเหตุสมผลตามบริบทธุรกิจ
│       └── การตรวจสอบการพยากรณ์ในเหตุการณ์พิเศษ
│
└── 6. การนำโมเดลไปใช้งาน
    ├── การพยากรณ์
    │   ├── Point forecasts (ค่าพยากรณ์เดี่ยว)
    │   ├── Prediction intervals
    │   │   ├── ช่วงความเชื่อมั่น (เช่น 80%, 95%)
    │   │   ├── การคำนวณแบบพารามิเตอร์ (parametric)
    │   │   └── การคำนวณแบบ bootstrap (non-parametric)
    │   └── Probabilistic forecasts
    │       ├── การพยากรณ์เป็นการแจกแจงความน่าจะเป็น
    │       └── ประโยชน์ในการวางแผนความเสี่ยง
    ├── การแปลงกลับ (inverse transformations)
    │   ├── กรณีใช้ Box-Cox
    │   │   ├── ต้องแปลงค่าพยากรณ์กลับด้วยค่า lambda เดิม
    │   │   └── ระวังผลกระทบต่อช่วงความเชื่อมั่น
    │   ├── กรณีใช้ log transformation
    │   │   ├── ใช้ exp() เพื่อแปลงกลับ
    │   │   └── ระวังความเอนเอียงเมื่อแปลงกลับ
    │   └── กรณีใช้ differencing
    │       ├── ต้องรวมผลต่างกลับเป็นค่าเดิม
    │       └── เริ่มจากค่าสุดท้ายของข้อมูลจริง
    ├── การบำรุงรักษาโมเดล
    │   ├── การฝึกใหม่ (retrain) เมื่อมีข้อมูลใหม่
    │   ├── การตรวจสอบประสิทธิภาพอย่างต่อเนื่อง
    │   └── การปรับโมเดลเมื่อมีการเปลี่ยนแปลงโครงสร้าง
    └── กรณีพิเศษ
        ├── การพยากรณ์ช่วงวิกฤต (เช่น COVID-19)
        ├── การพยากรณ์ช่วงเทศกาลและวันหยุด
        └── การรวมข้อมูลภายนอก (external data)
```

## 2. การพัฒนาโมเดล auto-SARIMA

```
การพัฒนาโมเดล auto-SARIMA สำหรับข้อมูลที่มีฤดูกาล
├── 1. การวิเคราะห์ลักษณะข้อมูลเบื้องต้น
│   ├── การตรวจสอบความมีฤดูกาล (Seasonality)
│   │   ├── วิเคราะห์ด้วยภาพ
│   │   │   ├── Time series plot
│   │   │   ├── Seasonal subseries plot
│   │   │   ├── Box plots by season
│   │   │   └── ACF plot (ค่านัยสำคัญที่ lag ฤดูกาล)
│   │   ├── การแยกองค์ประกอบฤดูกาล
│   │   │   ├── seasonal_decompose() จาก statsmodels
│   │   │   └── STL decomposition (Seasonal-Trend decomposition using LOESS)
│   │   └── การทดสอบความมีฤดูกาล
│   │       ├── Welch's ANOVA
│   │       ├── Kruskal-Wallis Test
│   │       └── QS (Quesenberry-Hurst) statistic
│   ├── การกำหนดความยาวฤดูกาล (m)
│   │   ├── m = 12 สำหรับข้อมูลรายเดือน
│   │   ├── m = 4 สำหรับข้อมูลรายไตรมาส
│   │   ├── m = 7 สำหรับข้อมูลรายวัน (รอบสัปดาห์)
│   │   ├── m = 24 สำหรับข้อมูลรายชั่วโมง (รอบวัน)
│   │   └── m = 168 สำหรับข้อมูลรายชั่วโมง (รอบสัปดาห์)
│   └── การเตรียมข้อมูล
│       ├── ตรวจสอบและจัดการข้อมูลที่หายไป
│       ├── ตรวจจับและจัดการค่าผิดปกติ
│       └── พิจารณาการแปลงข้อมูล
│
├── 2. การทดสอบความคงที่ (Stationarity) ในฤดูกาล
│   ├── แนวทางการทดสอบความคงที่ในฤดูกาล
│   │   ├── การพิจารณา ACF และ PACF ที่ lag ฤดูกาล
│   │   ├── การพล็อตข้อมูลแยกตามฤดูกาล
│   │   └── การทดสอบด้วยวิธีทางสถิติ
│   ├── การทดสอบ seasonal unit roots
│   │   ├── OCSB test (Osborn-Chui-Smith-Birchenhall)
│   │   │   ├── ใช้ใน pmdarima.nsdiffs()
│   │   │   ├── H₀: ข้อมูลมี seasonal unit root
│   │   │   └── ถ้าปฏิเสธ H₀ → ไม่จำเป็นต้องทำ seasonal differencing (D=0)
│   │   ├── Canova-Hansen test
│   │   │   ├── H₀: ฤดูกาลมีลักษณะเป็นแบบ deterministic
│   │   │   ├── ถ้าปฏิเสธ H₀ → ควรทำ seasonal differencing (D=1)
│   │   │   └── ใช้ใน pmdarima.nsdiffs() ด้วย method='ch'
│   │   ├── HEGY test (Hylleberg-Engle-Granger-Yoo)
│   │   │   ├── ตรวจสอบ unit roots ที่ความถี่ฤดูกาลต่างๆ
│   │   │   ├── สามารถตรวจจับ unit roots ที่บางความถี่ฤดูกาล
│   │   │   └── ใช้ SeasonalHEGYTest จาก arch library
│   │   └── การใช้ Augmented Dickey-Fuller test บนข้อมูลที่แยกเป็นฤดูกาล
│   └── การตัดสินใจค่า D (seasonal differencing order)
│       ├── D = 0 (ไม่ต้องทำ seasonal differencing)
│       ├── D = 1 (ทำ seasonal differencing 1 ครั้ง)
│       │   ├── คำนวณด้วย: y'ₜ = yₜ - yₜ₋ₘ
│       │   └── เพียงพอสำหรับข้อมูลในโลกจริงส่วนใหญ่
│       └── D > 1 (ไม่ค่อยพบในการใช้งานจริง)
│
├── 3. การหาค่า p, d, q (non-seasonal) และ P, Q (seasonal) ที่เหมาะสม
│   ├── การกำหนดค่า d ด้วยการทดสอบความคงที่
│   │   ├── นำข้อมูลที่ผ่านการทำ seasonal differencing แล้วมาทดสอบ
│   │   ├── ใช้ ADF, KPSS, หรือ PP test
│   │   └── พิจารณาค่า d ที่เหมาะสม (0, 1, หรือ 2)
│   ├── การใช้ auto-SARIMA จาก pmdarima
│   │   ├── กำหนดช่วงค่า p, q, P, Q ที่จะค้นหา
│   │   │   ├── start_p, max_p (ค่าเริ่มต้นและสูงสุดของ p)
│   │   │   ├── start_q, max_q (ค่าเริ่มต้นและสูงสุดของ q)
│   │   │   ├── start_P, max_P (ค่าเริ่มต้นและสูงสุดของ P)
│   │   │   └── start_Q, max_Q (ค่าเริ่มต้นและสูงสุดของ Q)
│   │   ├── กำหนดฤดูกาลและวิธีค้นหา
│   │   │   ├── seasonal=True (เปิดการค้นหาพารามิเตอร์ฤดูกาล)
│   │   │   ├── m=ค่าความยาวฤดูกาล
│   │   │   └── stepwise=True/False (ค้นหาแบบทีละขั้นหรือแบบละเอียด)
│   │   └── การกำหนดค่า d และ D
│   │       ├── d=None (ให้ auto_arima ค้นหาเอง)
│   │       ├── D=None (ให้ auto_arima ค้นหาเอง)
│   │       ├── max_d, max_D (จำกัดค่าสูงสุดที่จะพิจารณา)
│   │       └── หรือกำหนดค่าตายตัวจากการทดสอบก่อนหน้า
│   └── เทคนิคเพิ่มเติมในการกำหนดพารามิเตอร์
│       ├── การวิเคราะห์ ACF และ PACF ที่ลาก ฤดูกาล
│       │   ├── พิจารณา lag ที่เป็นจำนวนเท่าของ m
│       │   ├── ACF ที่ lag m, 2m, 3m... → บ่งชี้ค่า Q
│       │   └── PACF ที่ lag m, 2m, 3m... → บ่งชี้ค่า P
│       └── ใช้ statsmodels arma_order_select_ic หาค่า p และ q หลัง differencing
│
├── 4. การปรับแต่งและประเมินโมเดล auto-SARIMA
│   ├── การกำหนดเกณฑ์การเลือกโมเดล
│   │   ├── AIC (Akaike Information Criterion)
│   │   ├── AICc (corrected AIC)
│   │   ├── BIC (Bayesian Information Criterion)
│   │   └── HQIC (Hannan-Quinn Information Criterion)
│   ├── การตรวจสอบคุณภาพของโมเดล
│   │   ├── การวิเคราะห์ residuals
│   │   │   ├── ตรวจสอบความเป็น white noise
│   │   │   ├── ACF และ PACF ของ residuals
│   │   │   └── Ljung-Box test (portmanteau test)
│   │   ├── การตรวจสอบความแม่นยำในการพยากรณ์
│   │   │   ├── แบ่งข้อมูลเป็นชุดฝึกและชุดทดสอบ
│   │   │   ├── ใช้เมทริกซ์ MAE, RMSE, MAPE
│   │   │   └── Cross-validation แบบ time series
│   │   └── การเปรียบเทียบกับโมเดลอื่น
│   │       ├── Naive models (naive, seasonal naive)
│   │       ├── Exponential smoothing
│   │       └── ARIMA ไม่มีฤดูกาล
│   └── การปรับแต่งเพิ่มเติม
│       ├── การปรับปรุงการจับฤดูกาล
│       │   ├── ลองเปลี่ยนค่า m
│       │   ├── ลองเปลี่ยนวิธีการคำนวณ D (OCSB vs CH)
│       │   └── ตรวจสอบความถูกต้องของฤดูกาลที่กำหนด
│       ├── การเพิ่มตัวแปรภายนอก (exogenous variables)
│       │   ├── ตัวแปรดัมมี่สำหรับวันหยุด/เทศกาล
│       │   ├── ตัวแปรดัมมี่สำหรับเหตุการณ์พิเศษ
│       │   └── ตัวแปรที่มีผลต่อข้อมูล (เช่น อุณหภูมิต่อการใช้ไฟฟ้า)
│       └── การจัดการกับปัญหาเฉพาะ
│           ├── การจัดการกับวันหยุดเคลื่อนที่
│           ├── การจัดการกับการเปลี่ยนแปลงของฤดูกาลตามเวลา
│           └── การจัดการกับข้อมูลที่มีหลายฤดูกาลซ้อนกัน
│
└── 5. การพยากรณ์และการใช้งานโมเดล auto-SARIMA
    ├── การพยากรณ์ล่วงหน้า
    │   ├── Point forecasts
    │   ├── Prediction intervals
    │   │   ├── ช่วงความเชื่อมั่น 80%
    │   │   ├── ช่วงความเชื่อมั่น 95%
    │   │   └── วิธีคำนวณ confidence interval
    │   └── การแปลงกลับหลังการพยากรณ์
    │       ├── กรณีใช้การแปลง Box-Cox
    │       ├── กรณีใช้ log transformation
    │       └── การปรับแก้ความเอนเอียงจากการแปลงกลับ
    ├── การวิเคราะห์ผลการพยากรณ์
    │   ├── การพยากรณ์ในแต่ละฤดูกาล
    │   ├── การเปรียบเทียบกับข้อมูลจริง
    │   └── การวิเคราะห์ความแม่นยำตามระยะเวลาพยากรณ์
    └── การนำไปใช้งานจริง
        ├── การปรับปรุงโมเดลเมื่อมีข้อมูลใหม่
        ├── การใช้โมเดลในระบบอัตโนมัติ
        └── การรวมกับโมเดลอื่นๆ (ensemble forecasting)
```

## 3. ตัวอย่างโค้ดสำหรับการพัฒนาโมเดล auto-SARIMA

ตัวอย่างโค้ด Python สำหรับการพัฒนาโมเดล auto-SARIMA โดยใช้ pmdarima:

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import pmdarima as pm
from statsmodels.tsa.seasonal import seasonal_decompose
from statsmodels.graphics.tsaplots import plot_acf, plot_pacf

# 1. โหลดข้อมูล
# สมมติว่าข้อมูลอยู่ในไฟล์ CSV ที่มีการจัดเก็บเป็นรายชั่วโมง
df = pd.read_csv('power_production.csv', parse_dates=['timestamp'], index_col='timestamp')

# 2. วิเคราะห์ข้อมูลเบื้องต้น
plt.figure(figsize=(12, 6))
plt.plot(df.index, df['power_production'])
plt.title('Hourly Power Production')
plt.xlabel('Date')
plt.ylabel('Power Production (MW)')
plt.show()

# 3. แยกองค์ประกอบฤดูกาล
# ในที่นี้ใช้ m=24 สำหรับข้อมูลรายชั่วโมงที่มีรูปแบบรายวัน
decomposition = seasonal_decompose(df['power_production'], model='additive', period=24)
decomposition.plot()
plt.show()

# 4. ตรวจสอบ ACF และ PACF
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(12, 8))
plot_acf(df['power_production'], lags=48, ax=ax1)
plot_pacf(df['power_production'], lags=48, ax=ax2)
plt.show()

# 5. ทดสอบความคงที่และหาค่า d และ D ที่เหมาะสม
# ตรวจสอบความคงที่ด้วย ADF test
from pmdarima.arima.utils import ndiffs
from pmdarima.arima.utils import nsdiffs

# หาค่า D ที่เหมาะสม (seasonal differencing)
D = nsdiffs(df['power_production'], m=24, test='ocsb')
print(f"Suggested seasonal differencing order (D): {D}")

# ถ้า D > 0 ให้ทำ seasonal differencing ก่อน
if D > 0:
    df_diff = df['power_production'].diff(24).dropna()
else:
    df_diff = df['power_production']

# หาค่า d ที่เหมาะสม (regular differencing)
d = ndiffs(df_diff, test='adf')
print(f"Suggested regular differencing order (d): {d}")

# 6. พัฒนาโมเดล auto-SARIMA
# แบ่งข้อมูลเป็นชุดฝึกและชุดทดสอบ
train_size = int(len(df) * 0.8)
train, test = df.iloc[:train_size], df.iloc[train_size:]

# สร้างโมเดล auto-SARIMA
model = pm.auto_arima(train['power_production'],
                      seasonal=True,
                      m=24,  # ความยาวฤดูกาลรายวัน (24 ชั่วโมง)
                      start_p=1, start_q=1,
                      max_p=3, max_q=3,
                      start_P=0, start_Q=0,
                      max_P=2, max_Q=2,
                      d=d, D=D,  # ใช้ค่า d และ D ที่หาได้จากขั้นตอนก่อนหน้า
                      trace=True,
                      error_action='ignore',
                      suppress_warnings=True,
                      stepwise=True,
                      information_criterion='aic')

# แสดงสรุปโมเดล
print(model.summary())

# 7. พยากรณ์และประเมินผล
# พยากรณ์ข้อมูลในชุดทดสอบ
n_periods = len(test)
forecast, conf_int = model.predict(n_periods=n_periods, return_conf_int=True)

# สร้างกรอบข้อมูลสำหรับการพล็อต
forecast_index = test.index
forecast_series = pd.Series(forecast, index=forecast_index)

# พล็อตผลการพยากรณ์
plt.figure(figsize=(12, 6))
plt.plot(train.index, train['power_production'], label='Training Data')
plt.plot(test.index, test['power_production'], label='Actual')
plt.plot(forecast_index, forecast_series, label='Forecast')
plt.fill_between(forecast_index,
                 conf_int[:, 0],
                 conf_int[:, 1],
                 alpha=0.3, color='gray',
                 label='95% Confidence Interval')
plt.title('SARIMA Forecast vs Actual')
plt.legend()
plt.show()

# 8. ประเมินความแม่นยำ
from sklearn.metrics import mean_absolute_error, mean_squared_error

mae = mean_absolute_error(test['power_production'], forecast)
rmse = np.sqrt(mean_squared_error(test['power_production'], forecast))
mape = np.mean(np.abs((test['power_production'] - forecast) / test['power_production'])) * 100

print(f"Mean Absolute Error (MAE): {mae:.2f}")
print(f"Root Mean Squared Error (RMSE): {rmse:.2f}")
print(f"Mean Absolute Percentage Error (MAPE): {mape:.2f}%")

# 9. ตรวจสอบ residuals
residuals = model.resid()
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(12, 8))
plot_acf(residuals, lags=48, ax=ax1)
plot_pacf(residuals, lags=48, ax=ax2)
plt.show()

# Q-Q plot ของ residuals
from scipy import stats
import pylab
plt.figure(figsize=(10, 6))
stats.probplot(residuals, dist="norm", plot=pylab)
plt.title("Q-Q Plot of Residuals")
plt.show()

# Ljung-Box test
from statsmodels.stats.diagnostic import acorr_ljungbox
lb_test = acorr_ljungbox(residuals, lags=[24], return_df=True)
print(lb_test)

# 10. พยากรณ์ล่วงหน้า
future_forecast = model.predict(n_periods=168)  # พยากรณ์ล่วงหน้า 7 วัน (168 ชั่วโมง)
future_index = pd.date_range(start=df.index[-1], periods=169, freq='H')[1:]
future_series = pd.Series(future_forecast, index=future_index)

plt.figure(figsize=(12, 6))
plt.plot(df.index[-168:], df['power_production'][-168:], label='Historical Data')
plt.plot(future_index, future_series, label='Future Forecast')
plt.title('Power Production Forecast for Next 7 Days')
plt.legend()
plt.show()
```

หมายเหตุ: โค้ดตัวอย่างนี้อาจต้องปรับให้เหมาะกับลักษณะข้อมูลเฉพาะของคุณ เช่น การกำหนดค่า m ที่เหมาะสมกับลักษณะฤดูกาลของข้อมูล และการจัดการกับค่าผิดปกติหรือข้อมูลที่หายไป