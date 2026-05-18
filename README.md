# Medical Research Pipeline Plugin

ระบบครบวงจรสำหรับนักวิจัยทางการแพทย์ ตั้งแต่เตรียมข้อมูล วิเคราะห์สถิติ จนถึงเขียน manuscript เพื่อตีพิมพ์ใน Q1/Q2 journal

---

## Overview

Plugin นี้เชื่อมต่อ 3 workflow หลักเข้าด้วยกัน:

```
[ไฟล์ข้อมูล Excel]
    ↓ data-prep
[ข้อมูลสะอาด พร้อมวิเคราะห์]
    ↓ medical-stats
[ผลสถิติ Tables + Figures]
    ↓ manuscript-writer + manuscript-master-workflow
[Manuscript พร้อมส่ง Journal]
```

---

## Components

| Skill | Trigger | หน้าที่ |
|-------|---------|---------|
| `data-prep` | "เตรียมข้อมูล", "clean data", "จัดการ missing" | ทำความสะอาดข้อมูล Excel, วิเคราะห์ missing pattern, MICE imputation |
| `medical-stats` | "วิเคราะห์สถิติ", "statistical analysis", "Kaplan-Meier", "ROC" | วิเคราะห์สถิติ RCT/Cohort/Case-Control/Survival/Diagnostic Accuracy |
| `manuscript-writer` | "เขียน manuscript", "write paper", "เขียน Methods" | เขียน IMRAD draft, Tables, สร้าง results text พร้อมส่ง journal |

**Core Engine:** `manuscript-master-workflow` (ติดตั้งแยก) ทำหน้าที่ Develop / Peer Review / Language Refine / Journal Strategy

---

## Skills รายละเอียด

### 1. data-prep — เตรียมข้อมูล

**รองรับ file types:** Excel (.xlsx), CSV (.csv)

**ความสามารถ:**
- Data profiling: variable types, distributions, duplicates
- Missing data analysis: MCAR/MAR/MNAR classification, Little's test
- Imputation: MICE (Multiple Imputation by Chained Equations), KNN, mean/median/mode
- Outlier detection: IQR method, Winsorizing
- Variable coding: categorical encoding, ordinal mapping, cutpoint creation
- Export clean dataset + Data Preparation Report

**Trigger:** "เตรียมข้อมูล", "clean data", "ตรวจสอบ missing", "imputation", อัปโหลด Excel

### 2. medical-stats — วิเคราะห์สถิติ

**Study designs รองรับ:**
- **RCT / Cohort:** Table 1, t-test/Mann-Whitney, Chi-square/Fisher's, Logistic/Linear regression, ANOVA
- **Case-Control / Cross-sectional:** Odds Ratio, Rate Ratio, Multivariable logistic regression
- **Survival Analysis:** Kaplan-Meier, Log-rank test, Cox PH regression, Competing risks, Landmark analysis
- **Diagnostic Accuracy:** ROC/AUC, Sensitivity/Specificity/PPV/NPV, Likelihood ratios, DeLong test, Calibration, DCA

**Output:** Publication-ready tables, Figures (KM curve, ROC, Forest plot), Results text template

**Trigger:** "วิเคราะห์สถิติ", "Table 1", "Kaplan-Meier", "ROC curve", "logistic regression"

### 3. manuscript-writer — เขียน Manuscript

**Modes:**
- **Full IMRAD Draft:** เขียนครบทุก section จากข้อมูลที่มี
- **Single Section:** เขียนเฉพาะ section ที่ต้องการ
- **Tables & Figures Only:** สร้างเฉพาะ tables/figures/captions

**Reporting Guidelines:**
- CONSORT 2010 (RCT)
- STROBE 2007 (Observational)
- STARD 2015 (Diagnostic Accuracy)
- TRIPOD 2015 (Prediction Models)

**Integration:** เชื่อมกับ `manuscript-master-workflow` สำหรับ peer review, language refinement, journal strategy

---

## Typical Workflow

### สำหรับ RCT
```
1. อัปโหลดไฟล์ Excel → พิมพ์ "เตรียมข้อมูล"
2. ตรวจสอบ Data Preparation Report
3. พิมพ์ "วิเคราะห์สถิติ" → เลือก RCT
4. รับ Table 1, Primary/Secondary outcome tables, CONSORT numbers
5. พิมพ์ "เขียน manuscript"
6. รับ draft IMRAD
7. พิมพ์ "peer review manuscript" → manuscript-master-workflow Mode B
8. พิมพ์ "เลือก journal" → manuscript-master-workflow Mode D
```

### สำหรับ Survival Analysis
```
1. "เตรียมข้อมูล" + อัปโหลด Excel
2. "วิเคราะห์ survival" → KM curves, Log-rank, Cox PH
3. "เขียน Methods และ Results section"
4. "ช่วยพัฒนา Discussion" → manuscript-master-workflow Mode A
```

### สำหรับ Diagnostic Accuracy Study
```
1. "เตรียมข้อมูล"
2. "วิเคราะห์ diagnostic accuracy" → ROC, AUC, sensitivity/specificity
3. "เขียน manuscript ตาม STARD"
4. "เลือก journal" → manuscript-master-workflow Mode D
```

---

## Setup

Plugin นี้ไม่ต้องการ environment variables หรือ API keys

**Python packages ที่ต้องการ (Claude จะติดตั้งให้อัตโนมัติ):**
```bash
pip install pandas numpy scikit-learn missingno matplotlib seaborn openpyxl
pip install scipy statsmodels pingouin lifelines scikit-posthocs
```

---

## Dependencies

Plugin นี้ทำงานร่วมกับ **manuscript-master-workflow** skill ซึ่งต้องติดตั้งแยกต่างหาก
(มักติดตั้งมาพร้อมกันแล้ว)

---

## Author

Chagkrit — Medical Research Pipeline v0.1.0
