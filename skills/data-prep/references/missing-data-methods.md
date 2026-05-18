# Missing Data Methods — Reference Guide
# คู่มือวิธีการจัดการ Missing Data ในงานวิจัยทางการแพทย์

## 1. ทฤษฎีกลไก Missing Data (Rubin's Framework)

### MCAR — Missing Completely at Random
**นิยาม:** ความน่าจะเป็นที่ค่าจะ missing ไม่ขึ้นกับข้อมูลทั้งหมด (observed และ missing)
**ตัวอย่างทางคลินิก:** tube แตกโดยบังเอิญ, lab error แบบสุ่ม
**ผลกระทบ:** Complete case analysis ให้ unbiased estimates แต่สูญเสีย power
**ทดสอบ:** Little's MCAR Test (p > 0.05 = MCAR)

### MAR — Missing at Random
**นิยาม:** ความน่าจะเป็นที่จะ missing ขึ้นกับตัวแปรที่วัดได้ แต่ไม่ใช่ค่าที่ missing
**ตัวอย่างทางคลินิก:** ผู้ป่วยสูงอายุมักไม่กลับมา follow-up (อายุ = observed variable)
**ผลกระทบ:** CCA bias ได้; ต้องใช้ MI หรือ model-based methods
**assumption:** สามารถ condition บน observed data ได้

### MNAR — Missing Not at Random
**นิยาม:** ความน่าจะเป็นที่จะ missing ขึ้นกับค่าที่ไม่ได้วัดหรือค่าที่ missing เอง
**ตัวอย่างทางคลินิก:** ผู้ป่วยที่ pain score สูงมากเลิกมา follow-up
**ผลกระทบ:** อันตรายที่สุด — ทุกวิธีให้ biased results ถ้าไม่จัดการถูกต้อง
**approach:** Pattern mixture models, Selection models, Sensitivity analysis

---

## 2. วิธีการ Imputation

### 2.1 Complete Case Analysis (Listwise Deletion)
```python
df_complete = df.dropna()
```
**ใช้เมื่อ:** MCAR, missing < 5%
**ข้อดี:** ง่าย, valid ถ้า MCAR จริง
**ข้อเสีย:** สูญเสีย sample size, power ลดลง

### 2.2 Single Imputation Methods
```python
# Mean imputation (continuous, normal distribution)
df['variable'].fillna(df['variable'].mean(), inplace=True)

# Median imputation (continuous, skewed)
df['variable'].fillna(df['variable'].median(), inplace=True)

# Mode imputation (categorical)
df['variable'].fillna(df['variable'].mode()[0], inplace=True)

# Last Observation Carried Forward (LOCF) - longitudinal data
df['variable'] = df.groupby('patient_id')['variable'].fillna(method='ffill')
```
**ใช้เมื่อ:** Missing < 5%, exploratory analysis
**ข้อเสีย:** underestimate variance, distort relationships

### 2.3 Multiple Imputation by Chained Equations (MICE)
วิธีมาตรฐานทองสำหรับ MAR
```python
from sklearn.experimental import enable_iterative_imputer
from sklearn.impute import IterativeImputer
import numpy as np

# MICE implementation
mice_imputer = IterativeImputer(
    max_iter=10,          # จำนวน cycles
    n_nearest_features=None,  # ใช้ทุก feature
    random_state=42,
    verbose=0
)

# แยก numeric columns ก่อน
df_numeric = df.select_dtypes(include=[np.number])
df_imputed_array = mice_imputer.fit_transform(df_numeric)
df_imputed = pd.DataFrame(df_imputed_array, columns=df_numeric.columns)

# รวมกลับกับ categorical columns
df_categorical = df.select_dtypes(exclude=[np.number])
df_final = pd.concat([df_imputed, df_categorical], axis=1)
```
**Pooling rules (Rubin's rules):** ใช้ m ≥ 5 datasets (แนะนำ m = 20 สำหรับ missing > 10%)

### 2.4 KNN Imputation
```python
from sklearn.impute import KNNImputer

knn_imputer = KNNImputer(
    n_neighbors=5,        # ปรับตาม sample size
    weights='uniform',    # หรือ 'distance'
    metric='nan_euclidean'
)
df_knn = pd.DataFrame(knn_imputer.fit_transform(df_numeric), columns=df_numeric.columns)
```
**ดีกว่า MICE เมื่อ:** ความสัมพันธ์ไม่เป็น linear, ข้อมูล mixed types

### 2.5 สำหรับ MNAR — Sensitivity Analysis
```python
# Delta adjustment method
# สมมติว่า missing values มีค่าต่ำกว่า observed ค่า delta หน่วย
delta_values = [0, -0.5, -1.0, -1.5, -2.0]  # range of assumptions
results = {}
for delta in delta_values:
    df_sensitivity = df.copy()
    missing_mask = df_sensitivity['outcome'].isnull()
    # impute ก่อน แล้วค่อย adjust
    df_sensitivity.loc[missing_mask, 'outcome'] = \
        df_imputed.loc[missing_mask, 'outcome'] + delta
    # วิเคราะห์และเก็บผล
    results[delta] = run_analysis(df_sensitivity)
```

---

## 3. การเลือกวิธีตามบริบท

### สำหรับ RCT (ตาม CONSORT 2010 extension)
| สถานการณ์ | วิธีที่ CONSORT แนะนำ |
|---|---|
| Primary outcome, MAR | Multiple Imputation (MICE) |
| ITT analysis | MICE หรือ BOCF/WOCF |
| Per-protocol | Complete Case Analysis |
| MNAR suspected | Sensitivity analysis ด้วย tipping point |

### สำหรับ Observational Studies (STROBE)
- รายงาน % missing ทุก key variable ใน Table 1
- ระบุ method ใน Methods section
- แสดง sensitivity analysis ใน supplementary

### สำหรับ Survival Analysis
```python
# Missing ใน time-to-event: ห้าม impute event/time
# Missing ใน covariates: MICE ได้
# ใช้ survminer package ใน R หรือ lifelines ใน Python
```

### สำหรับ Diagnostic Accuracy (STARD)
- ห้าม impute index test หรือ reference standard
- Missing ใน covariates สามารถ impute ได้
- รายงาน flow diagram ชัดเจน

---

## 4. Validation — ตรวจสอบ Imputation Quality

```python
import matplotlib.pyplot as plt
import seaborn as sns

# เปรียบเทียบ distribution ก่อนและหลัง
fig, axes = plt.subplots(1, 2, figsize=(12, 5))

# Original (observed only)
axes[0].hist(df['variable'].dropna(), bins=30, alpha=0.7, label='Observed', color='blue')
axes[0].set_title('Original (observed values)')

# After imputation
axes[1].hist(df_imputed['variable'], bins=30, alpha=0.7, label='Imputed', color='orange')
axes[1].set_title('After MICE imputation')

plt.tight_layout()
plt.savefig('imputation_comparison.png', dpi=150)
```

**สิ่งที่ต้องตรวจสอบ:**
- Distribution shape ควรคล้ายเดิม (ไม่ควรสร้าง bimodal จากที่ไม่มี)
- Mean/Median/SD ไม่ควรเปลี่ยนมากกว่า 10-15%
- Correlation ระหว่างตัวแปรควรคงเดิม

---

## 5. การรายงานใน Manuscript

### Methods Section Template
```
Missing data analysis revealed that [variable_1] had [X]% missing values and
[variable_2] had [Y]% missing. Little's MCAR test indicated data were [MCAR/not MCAR]
(χ² = [value], df = [df], p = [p-value]). Multiple imputation using chained equations
(MICE) was performed with [m] imputed datasets, pooled using Rubin's rules.
[OR: Complete case analysis was performed as missing data were completely at random
and constituted less than 5% of observations.] Sensitivity analyses comparing
complete case and imputed results are presented in Supplementary Table [X].
```

### Table 1 Format (แสดง missing)
```
Variable          | n (%) available | Missing n (%)
Age (years)       | 250 (100%)      | 0 (0%)
BMI               | 238 (95.2%)     | 12 (4.8%)
Baseline HbA1c    | 225 (90.0%)     | 25 (10.0%)
```

---

## 6. Package Requirements

```bash
pip install pandas numpy scikit-learn missingno matplotlib seaborn openpyxl
# สำหรับ statistical testing
pip install scipy statsmodels pingouin
```
