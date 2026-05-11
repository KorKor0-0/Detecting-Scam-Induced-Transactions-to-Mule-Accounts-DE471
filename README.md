# Detecting scam-Induced transactions to mule Accounts
## Background & Pain Points
- **The Problem**: ปัจจุบันมิจฉาชีพเปลี่ยนรูปแบบจากการเจาะระบบ (Traditional Fraud) มาเป็นการหลอกลวงให้เหยื่อโอนเงินด้วยตนเอง เข้าสู่ "บัญชีม้า"
- **Legacy System Limitations**: ระบบป้องกันภัยแบบเดิมของธนาคารมักตรวจจับจาก "อุปกรณ์ที่ผิดปกติ" หรือ "การเข้าสู่ระบบที่น่าสงสัย" แต่ในกรณี APP Fraud ธุรกรรมเหล่านี้ถูกทำขึ้นโดยตัวลูกค้าเองบนอุปกรณ์เดิมและผ่านการยืนยันตัวตน (เช่น สแกนหน้า/OTP) ทำให้ระบบเดิมไม่สามารถตรวจจับได้
- **Business Impact**: การที่ธนาคารไม่สามารถตรวจจับและระงับเหตุได้ทันท่วงที
  นำไปสู่ความสูญเสียทางการเงินมหาศาลของลูกค้า (Financial Loss)
  ทำลายความน่าเชื่อถือของธนาคาร (Reputational Damage)
  และอาจนำไปสู่การถูกตรวจสอบหรือปรับจากหน่วยงานกำกับดูแล (Compliance Risk)
 ---
## SMART Objectives 
เพิ่มอัตราการตรวจจับทุจริต (Recall) ให้ ≥85% และลดอัตราการแจ้งเตือนผิดพลาด (False Positive Rate) 
ลงโดยอาศัยการวิเคราะห์พฤติกรรมการโอนเงินออกที่ผิดปกติ (Abnormal outflow behavior) 

---
## Questions

- Q1: บัญชีที่มีความเสี่ยงสูง มีสัดส่วนการโอนเงินออก (Outflow Ratio) มากกว่า 80% แตกต่างจากบัญชีปกติอย่างมีนัยสำคัญหรือไม่ และสามารถใช้เป็นตัวบ่งชี้พฤติกรรมบัญชีม้าได้หรือไม่?
- Q2: ธุรกรรมที่เกิดขึ้นในช่วงเวลาสั้นหลังจากได้รับเงิน มีรูปแบบความเร็ว และความถี่แตกต่างจากบัญชีปกติอย่างไร และสะท้อนพฤติกรรมของบัญชีม้าได้หรือไม่?
- Q3: ธุรกรรมจากพื้นที่หรืออุปกรณ์ที่มีความเสี่ยงสูง เพิ่มความน่าจะเป็นของการทุจริตหรือไม่?
---
## Questions
- H1: บัญชีที่มี Outflow Ratio > 80% มีแนวโน้มเป็นบัญชีม้าสูงกว่าบัญชีปกติอย่างมีนัยสำคัญ
- H2: บัญชีม้ามี Transaction Velocity สูง และใช้เวลาโอนออกสั้นมาก (Short Time Delta) ทันทีที่ได้รับเงินเข้า
- H3: การทำธุรกรรมจากอุปกรณ์หรือพื้นที่เสี่ยงสูง (High-risk location) มีความเชื่อมโยงกับ Outflow Ratio ที่สูง และเพิ่มโอกาสเกิด Fraud อย่างชัดเจน
---
## Data Dictionary
This dataset was generated using a structured prompt to simulate realistic Thai banking behavior for early detection of scam-induced transactions.
- Cleaned synthetic dataset (no missing or inconsistent data)
- Designed for behavioral analysis and fraud detection
- Supports BI tools such as Tableau
- Over 20,000 transaction records
- Proper relational integrity across all tables
### Dataset Structure
***Transactions Table***
| Attributes | Data Type | Description | Example |
|-------------|-----------|-------------|-------------|
| transaction_id | VARCHAR(20) | Unique identifier for each transaction |TXN00000001, TXN00000002|
| timestamp | DATETIME | Date and time when the transaction occurred | 1/1/2025 8:07 |
| account_id | VARCHAR(20) | Account ID of the customer who initiated the transaction | ACC-8184053697 |
| device_id | VARCHAR(20) | ID of the device used to perform the transaction | DEV-64cfd9b9 |
| transaction_type | VARCHAR(20) | Type of the transaction |Payment, Deposit, Transfer In, Transfer Out, Withdraw|
| amount | DECIMAL(15,2) | Transaction amount in Thai Baht |2426.42, 12000|
| balance_after | DECIMAL(15,2) | Account balance after the transaction is completed |23615.58, 34593|
| counterparty_bank | VARCHAR(50) |Bank or channel name of the counterparty|SCB, BBL, KTB, Payment Gateway|
| counterparty_account | VARCHAR(50) |Account number or ID of the counterparty|299-2-81028-8, SHOP-6935|
| location_region | VARCHAR(50) |Province where the transaction took place|Phuket, Songkhla|
| risk_level | VARCHAR(20) |Risk level of the transaction assessed by the Fraud Detection system|Normal, Medium, High|
---
***Accounts Table***
| Attributes | Data Type | Description | Example |
|-------------|-----------|-------------|-------------|
|account_id  |VARCHAR(20)|Unique identifier for each bank account|ACC-4163119785, ACC-2801823908|
| customer_id |VARCHAR(10)|ID of the customer who owns the account|CUS00001, CUS00004|
| account_type |VARCHAR(20)|Type of the bank account|Savings, Current, Fixed Deposit, Payroll|
| account_open_date |DATE|Date when the account was officially opened|28/4/2023, 13/3/2024|
---
***Customers Table***
| Attributes | Data Type | Description | Example |
|-------------|-----------|-------------|-------------|
| customer_id |VARCHAR(10)|Unique identifier for each customer|CUS00001, CUS00002|
| full_name |VARCHAR(100)|Full name of the customer|Allison Hill, Brandon Davis|
| age |INT|Age of the customer in years|25, 46, 60|
| occupation |VARCHAR(50)|Customer's current occupation|Engineer, Manager, Freelance, Student|
| estimated_income |DECIMAL(12,2)|Customer's estimated monthly income in Thai Baht|56000, 84000, 126000|
| home_province |VARCHAR(50)|Province of the customer's home address|Bangkok, Phuket, Songkhla|
---
***Devices  Table***
| Attributes | Data Type | Description | Example |
|-------------|-----------|-------------|-------------|
| device_id |VARCHAR(20)|Unique identifier for each registered device|DEV-1a3d1fa7, DEV-17fc695a|
| customer_id |VARCHAR(10)|ID of the customer who owns the device|CUS00001, CUS00002|
| device_model|VARCHAR(50)|Model name of the registered device|Samsung S24, iPhone 14|
| os_version |VARCHAR(20)|Operating system of the device|Android, iOS|
| is_rooted |BOOLEAN|Indicates whether the device has been rooted or jailbroken|TRUE, FALSE|
---
## Data Quality & Cleaning
- **Data Integrity & Quality Check**: ตรวจสอบความครบถ้วนของข้อมูล (Missing Values) 
และกำจัดข้อมูลซ้ำซ้อนโดยใช้ transaction_id เป็น Unique Identifier 
เพื่อป้องกันความคลาดเคลื่อนในการวิเคราะห์เชิงสถิติ
- **Structural & Logical Correction**: ระบุและแก้ไขข้อมูลที่ผิดตรรกะทางธุรกิจ เช่น ยอดเงินติดลบ หรือรูปแบบวันเวลา (Timestamp)
ที่ไม่อยู่ในขอบเขตการวิเคราะห์ เพื่อคงความถูกต้องของโครงสร้างข้อมูล (Data Integrity)
- **Standardization & Consistency**: ปรับมาตรฐานรูปแบบตัวแปร (เช่น ชื่อธนาคาร, ประเภทธุรกรรม)
ให้เป็นรูปแบบเดียวกันทั้งชุดข้อมูล และกำหนด Data Type ให้ถูกต้อง (Datetime, Numeric) เพื่อความพร้อมในการประมวลผล
- **Strategic Outlier & Signal Preservation**: "ไม่ลบค่า Outliers ทิ้ง เนื่องจากค่าที่สูงผิดปกติ (เช่น การโอนเงินถี่หรือยอดสูง)
คือ สัญญาณหลัก (Fraud Signals) ที่ใช้บ่งชี้พฤติกรรมบัญชีม้า โดยมุ่งเน้นการขจัด Noise แต่ยังคงรักษาพฤติกรรมสำคัญของธุรกรรมทุจริตไว้ครบถ้วน

****

****


  
