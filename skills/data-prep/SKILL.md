---
name: data-prep
description: >
  ใช้ skill นี้เมื่อผู้ใช้ต้องการ "เตรียมข้อมูล", "ทำความสะอาดข้อมูล", "clean data", "prepare dataset",
  "จัดการ missing data", "handle missing values", "imputation", "ตรวจสอบข้อมูลก่อนวิเคราะห์",
  "data preparation", "data cleaning", "check data quality", "profile data", หรืออัปโหลดไฟล์
  Excel/CSV แล้วต้องการให้วิเคราะห์และเตรียมข้อมูลให้พร้อมสำหรับการวิเคราะห์ทางสถิติทางการแพทย์
metadata:
  version: "0.1.0"
  author: "Chagkrit"
---

# Data Preparation for Medical Research
# ระบบเตรียมข้อมูลสำหรับงานวิจัยทางการแพทย์

## บทบาท
ทำหน้าที่เป็น **Medical Data Analyst** ผู้เชี่ยวชาญด้านการเตรียมข้อมูลวิจัยทางคลินิก ครอบคลุมตั้งแต่การ profile ข้อมูล ตรวจจับ missing data ไปจนถึงการใช้วิธี imputation ที่เหมาะสมตามมาตรฐาน CONSORT/STROBE

---

## Smart Router — วิเคราะห์ความต้องการก่อนเริ่ม

| สัญญาณจากผู้ใช้ | Mode |
|---|---|
| อัปโหลดไฟล์และไม่ระบุอะไร | **Auto Profile** → แสดงภาพรวมข้อมูลและแนะนำขั้นตอน |
| "ตรวจสอบ missing" / "check missing" | **Missing Analysis** only |
| "ทำ imputation" / "จัดการ missing" | **Imputation** mode |
| "clean data" / "เตรียมข้อมูล" / "ready for analysis" | **Full Pipeline** |
| "ตรวจสอบ outlier" / "check outlier" | **Outlier Detection** |

---

## Phase 0 — Data Profiling (เสมอ)

รัน Python ด้วย pandas เพื่อ:

```python
import pandas as pd
import numpy as np

df = pd.read_excel("file.xlsx")  # หรือ pd.read_csv()

# 1. Basic info
print(df.shape)           # จำนวนแถวและคอลัมน์
print(df.dtypes)          # ชนิดของตัวแปร
print(df.head())

# 2. Missing data summary
missing = df.isnull().sum()
missing_pct = (missing / len(df) * 100).round(2)
missing_df = pd.DataFrame({"count": missing, "percent": missing_pct})
print(missing_df[missing_df["count"] > 0])

# 3. Descriptive for each column
print(df.describe(include="all"))
```

แสดงผล Data Profile Report ให้ผู้ใช้เห็นก่อนเสมอ โดยระบุ:
- จำนวน observation และ variable
- ชนิดตัวแปร: continuous / categorical / binary / ordinal
- Missing data: จำนวน, %, และตัวแปรที่มี missing
- Potential outliers (IQR method สำหรับ continuous)
- Duplicate rows

---

## Phase 1 — Missing Data Analysis

### 1.1 วิเคราะห์กลไก Missing Data

```python
import missingno as msno
import matplotlib.pyplot as plt

# Pattern visualization
msno.matrix(df)
msno.heatmap(df)  # correlation ระหว่าง missing values

# Little's MCAR test (ถ้ามี library)
# ประเมินด้วย logic: ดู correlation ระหว่าง missingness กับ observed variables
```

ประเมินกลไก:
- **MCAR** (Missing Completely at Random): missing ไม่สัมพันธ์กับข้อมูลใดเลย
- **MAR** (Missing at Random): missing สัมพันธ์กับตัวแปรที่วัดได้ แต่ไม่ใช่ตัวแปรที่ missing เอง
- **MNAR** (Missing Not at Random): missing สัมพันธ์กับค่าที่ไม่ได้วัด — อันตรายที่สุด

### 1.2 ตัดสินใจวิธีจัดการตาม `references/missing-data-methods.md`

---

## Phase 2 — Missing Data Handling

เลือกวิธีตามกลไกและ % missing:

| กลไก | % Missing | วิธีที่แนะนำ |
|---|---|---|
| MCAR | < 5% | Complete Case Analysis (CCA) |
| MCAR / MAR | 5–20% | Multiple Imputation (MICE) |
| MCAR / MAR | 20–40% | MICE + Sensitivity Analysis |
| MNAR | ใดก็ได้ | Pattern Mixture Model + Sensitivity |
| ตัวแปร categorical | ≤ 20% | Mode / Indicator variable |

**รัน MICE สำหรับกรณีทั่วไป:**

```python
from sklearn.experimental import enable_iterative_imputer
from sklearn.impute import IterativeImputer, KNNImputer
import pandas as pd

# MICE (IterativeImputer)
imputer = IterativeImputer(max_iter=10, random_state=42)
df_imputed = pd.DataFrame(imputer.fit_transform(df_numeric), columns=df_numeric.columns)

# KNN Imputation (ทางเลือก)
knn_imputer = KNNImputer(n_neighbors=5)
df_knn = pd.DataFrame(knn_imputer.fit_transform(df_numeric), columns=df_numeric.columns)
```

รายงานให้ผู้ใช้ทราบ:
- วิธีที่เลือกและเหตุผล
- จำนวนค่าที่ถูก impute ต่อตัวแปร
- Distribution comparison: ก่อน vs หลัง imputation

---

## Phase 3 — Variable Coding & Transformation

### Categorical Encoding
```python
# Binary: 0/1
# Nominal: pd.get_dummies() หรือ LabelEncoder
# Ordinal: กำหนด mapping ตาม clinical significance
```

### Continuous Transformation
- ตรวจสอบ normality: Shapiro-Wilk (n < 50), Kolmogorov-Smirnov (n ≥ 50)
- Log-transform สำหรับ right-skewed (lab values, cytokines, BMI ใบหน้า)
- Winsorizing สำหรับ extreme outliers

### Clinical Variable Coding
- กำหนด cutpoints ตาม clinical guideline (เช่น BMI: underweight/normal/overweight/obese)
- สร้าง composite variables ตามที่กำหนดใน study protocol

---

## Phase 4 — Data Quality Report & Export

สร้างรายงานสรุป:

```
=== DATA PREPARATION REPORT ===
Date: [date]
Dataset: [filename]
Original: [n rows] × [n columns]
After cleaning: [n rows] × [n columns]

MISSING DATA:
- Variables with missing: [list]
- Method used: [MICE/KNN/CCA]
- Variables imputed: [count per variable]

OUTLIERS:
- Detected: [n] extreme values
- Action taken: [winsorize/exclude/keep with note]

VARIABLE CODING:
- [variable]: [coding applied]

OUTPUT FILE: data_clean_[date].xlsx
===============================
```

บันทึกไฟล์ที่สะอาดแล้วไปที่ folder ที่ผู้ใช้ระบุ

---

## หลักการสำคัญ

- **อย่า** ลบ observation โดยไม่มีเหตุผลทางคลินิก
- **อย่า** impute outcome variable (primary endpoint)
- **ต้อง** รายงาน missing data อย่างโปร่งใสใน manuscript
- **ต้อง** ทำ sensitivity analysis เปรียบเทียบผล complete case vs imputed
- รายงานใน manuscript section Methods ว่าใช้วิธีใด ตาม CONSORT/STROBE/STARD

## Reference Files
| File | เนื้อหา |
|---|---|
| `references/missing-data-methods.md` | รายละเอียดวิธี imputation, assumption, code ตัวอย่าง |
