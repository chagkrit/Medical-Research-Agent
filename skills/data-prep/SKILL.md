---
name: data-prep
description: >
  ใช้ skill นี้เมื่อผู้ใช้ต้องการ "เตรียมข้อมูล", "ทำความสะอาดข้อมูล", "clean data", "prepare dataset",
  "จัดการ missing data", "handle missing values", "imputation", "MICE", "MNAR sensitivity",
  "ตรวจสอบข้อมูลก่อนวิเคราะห์", "data preparation", "data cleaning", "check data quality",
  "profile data", "audit trail", "CONSORT flow diagram", "STROBE flow", "data dictionary",
  "data sharing statement", หรืออัปโหลดไฟล์ Excel/CSV/SAS/SPSS แล้วต้องการให้วิเคราะห์และเตรียมข้อมูลให้พร้อม
  สำหรับการตีพิมพ์ใน Q1/Q2 high-impact-factor journal (CONSORT/STROBE/STARD/TRIPOD-compliant)
metadata:
  version: "1.0.0"
  author: "Chagkrit"
  target_tier: "Q1 high-impact-factor"
---

# Data Preparation for Q1/Q2 High-Impact Medical Research
# ระบบเตรียมข้อมูลสำหรับงานวิจัยทางการแพทย์ระดับ Q1/Q2

## บทบาท
ทำหน้าที่เป็น **Senior Clinical Data Analyst** สำหรับงานวิจัยที่จะตีพิมพ์ใน **Q1/Q2 journal ที่มี high impact factor** (Lancet, NEJM, JAMA, BMJ, Nature Medicine tier)

ครอบคลุมตั้งแต่ data profiling, missing-data classification (MCAR/MAR/MNAR), multiple imputation, outlier handling, variable coding, audit trail ไปจนถึง pre-specified analysis dataset (analysis dataset locked → ITT / PP / mITT) และ data dictionary ที่พร้อมแนบเป็น supplementary material

---

## Quality Bar — Q1 High-IF Standards

| ข้อกำหนด | มาตรฐาน |
|---|---|
| Reporting guideline | CONSORT 2010 §13–14 · STROBE 2007 §13 · STARD 2015 §16 · TRIPOD 2015 §9 |
| Missing data | ต้องระบุกลไก (MCAR/MAR/MNAR) + sensitivity analysis (pre-specified) |
| Imputation | Multiple Imputation (MICE) m ≥ 20 · Rubin's rules · convergence diagnostics |
| Data dictionary | ทุก variable มี name, type, unit, allowed values, derivation rule |
| Audit trail | ทุก transformation มี log + reproducible code |
| Pre-specification | analysis plan locked ก่อน unblinding (RCT) |
| Data sharing | ICMJE-compliant data sharing statement ทุก submission |

---

## Smart Router

| Trigger | Mode |
|---|---|
| Upload file ครั้งแรก (ไม่ระบุอะไร) | **Mode 0 — Auto Profile** |
| "ตรวจสอบ missing" / "check missing" | **Mode 1 — Missing Analysis** |
| "ทำ imputation" / "MICE" / "MNAR sensitivity" | **Mode 2 — Imputation** |
| "clean data" / "เตรียมข้อมูล" / "full pipeline" | **Mode 3 — Full Pipeline** |
| "ตรวจสอบ outlier" / "winsorize" | **Mode 4 — Outlier Detection** |
| "data dictionary" / "codebook" | **Mode 5 — Data Dictionary Export** |
| "CONSORT flow" / "STROBE flow" | **Mode 6 — Flow Diagram Numbers** |
| "audit trail" / "reproducibility log" | **Mode 7 — Audit Trail Report** |

---

## Mode 0 — Auto Profile (เสมอ ก่อน mode อื่น)

```python
import pandas as pd
import numpy as np
from datetime import datetime

# Load (รองรับ .xlsx, .csv, .dta (Stata), .sav (SPSS))
ext = filename.split('.')[-1].lower()
if ext == 'xlsx':
    df = pd.read_excel(filename)
elif ext == 'csv':
    df = pd.read_csv(filename)
elif ext == 'dta':
    df = pd.read_stata(filename)
elif ext == 'sav':
    df = pd.read_spss(filename)

# 1. Shape & dtypes
print(f"Observations: {df.shape[0]:,}  Variables: {df.shape[1]}")
print(df.dtypes.value_counts())

# 2. Missing summary
missing = df.isnull().sum()
missing_pct = (missing / len(df) * 100).round(2)
missing_df = (pd.DataFrame({"count": missing, "percent": missing_pct})
              .query("count > 0").sort_values("percent", ascending=False))

# 3. Duplicate rows
print(f"Duplicate rows: {df.duplicated().sum()}")

# 4. Auto-detected variable type
def classify_var(s):
    if pd.api.types.is_numeric_dtype(s):
        nunique = s.nunique()
        if nunique == 2:    return "binary"
        if nunique <= 10:   return "ordinal/categorical-numeric"
        return "continuous"
    return "categorical"

types = {col: classify_var(df[col]) for col in df.columns}

# 5. Descriptive
desc = df.describe(include='all').T
```

แสดง **Data Profile Report (TH/EN bilingual)** ให้ผู้ใช้:

```
═══════════════════════════════════════════════════════════════════
📊 DATA PROFILE REPORT
═══════════════════════════════════════════════════════════════════
Dataset           : <filename>
Loaded            : <date>
Observations (n)  : <n>
Variables (p)     : <p>
Variable types    : continuous=<c>, binary=<b>, ordinal=<o>, categorical=<cat>
Duplicate rows    : <d>
Missing variables : <m>/<p> (<pct>%)
Date range (if dt): <min>–<max>

🚦 RECOMMENDED NEXT STEP
─────────────────────────────────────────────────────────────────
[ ] Mode 1 — Missing Analysis  (>0 missing → required)
[ ] Mode 3 — Full Pipeline     (clean + impute + code + export)
[ ] Mode 5 — Data Dictionary   (codebook สำหรับ supplementary)
═══════════════════════════════════════════════════════════════════
```

---

## Mode 1 — Missing Data Analysis (Q1-grade)

### 1.1 Pattern visualization
```python
import missingno as msno
import matplotlib.pyplot as plt

fig, axes = plt.subplots(1, 3, figsize=(18, 5))
msno.matrix(df, ax=axes[0]); axes[0].set_title("Missing Matrix")
msno.bar(df, ax=axes[1]);    axes[1].set_title("Missing per variable")
msno.heatmap(df, ax=axes[2]); axes[2].set_title("Missing correlation")
plt.tight_layout(); plt.savefig("missing_pattern.png", dpi=300)
```

### 1.2 Mechanism classification (MCAR / MAR / MNAR)

```python
# (a) Little's MCAR test (preferred; statsmodels does not implement → use 'naniar' R or 'pyampute')
# Approximate Little's test in Python:
from scipy.stats import chi2

def little_mcar_test(df_num):
    """Likelihood-ratio test for MCAR (Little, 1988)."""
    # ... (โหลด implementation จาก naniar-style)
    # Output: chi2, df, p-value
    pass

# (b) Logistic regression: predict missingness from observed variables
from sklearn.linear_model import LogisticRegression

for col in df.columns[df.isnull().any()]:
    y = df[col].isnull().astype(int)
    X = df.drop(columns=[col]).select_dtypes(include=[np.number]).fillna(df.median(numeric_only=True))
    if X.shape[1] == 0:
        continue
    lr = LogisticRegression(max_iter=1000).fit(X, y)
    score = lr.score(X, y)
    print(f"{col}: missing-vs-observed pseudo-R² ≈ {score:.3f}")
    # high score → missingness depends on observed → MAR (not MCAR)
```

| Mechanism | Definition | Decision Rule | Action |
|---|---|---|---|
| **MCAR** | missing independent of all data | Little's p > 0.05 + logistic AUC ≈ 0.5 | CCA acceptable if missing < 5% |
| **MAR** | missing depends on observed variables only | Little's p < 0.05, AUC > 0.6 | **MICE** (Q1 default) |
| **MNAR** | missing depends on unobserved value | suspected from clinical context | **MICE + Pattern Mixture / Delta-adjustment sensitivity** |

### 1.3 Q1-grade reporting template (paste-ready for Methods)

```
"Missingness was assessed using Little's MCAR test and logistic
regression of missingness on observed variables. Variables with
≥5% missing were handled by multiple imputation by chained
equations (MICE, m = 20 imputations, 10 iterations each) under
the missing-at-random assumption (van Buuren, 2018). Sensitivity
to a missing-not-at-random departure was assessed using a
delta-adjustment of ±X SD applied to the imputed values
(Cro et al., 2020). Estimates from imputed datasets were pooled
using Rubin's rules."
```

---

## Mode 2 — Multiple Imputation (MICE)

### 2.1 Method matrix

| Mechanism | % missing | Method | m | Notes |
|---|---|---|---|---|
| MCAR | < 5% | CCA acceptable | — | Report ITT denominators |
| MCAR/MAR | 5–40% | **MICE (default)** | **m ≥ 20** | Predictive mean matching for skewed |
| MAR | 40–60% | MICE + caution + extended sensitivity | m = 50 | Triangulate with CCA |
| MNAR | any | MICE + Pattern Mixture / Tipping-point | m ≥ 20 | Pre-specify δ in protocol |
| Auxiliary vars helpful | any | MICE with auxiliary variables | m = 20 | Improves MAR plausibility |

> **NEVER impute the primary outcome** in a confirmatory analysis without an explicit sensitivity plan.

### 2.2 MICE code (Q1-grade, m=20, with pooling)

```python
from sklearn.experimental import enable_iterative_imputer
from sklearn.impute import IterativeImputer
from sklearn.ensemble import RandomForestRegressor
import numpy as np

# (a) Choose imputer per variable type — pmm via RandomForest works well for non-normal
m = 20  # number of imputations (Q1 default)

imputations = []
for seed in range(m):
    imp = IterativeImputer(
        estimator=RandomForestRegressor(n_estimators=10, random_state=seed),
        max_iter=10,
        sample_posterior=False,
        random_state=seed,
    )
    df_imp = pd.DataFrame(imp.fit_transform(df_numeric),
                          columns=df_numeric.columns, index=df_numeric.index)
    imputations.append(df_imp)

# (b) Convergence: plot mean of imputed variables across iterations
# (c) Pool estimates with Rubin's rules
def rubin_pool(point_estimates, variances):
    Q_bar = np.mean(point_estimates)
    U_bar = np.mean(variances)
    B = np.var(point_estimates, ddof=1)
    T = U_bar + (1 + 1/len(point_estimates)) * B
    se = np.sqrt(T)
    return Q_bar, se
```

> สำหรับโครงสร้างที่ซับซ้อน → ใช้ **`miceforest`** package (Q1 reviewers ยอมรับ):
> `pip install miceforest --break-system-packages`

### 2.3 MNAR sensitivity (tipping-point / delta adjustment)

```python
for delta in [-1.0, -0.5, 0.0, 0.5, 1.0]:  # SD shift on imputed values
    df_sens = imputations[0].copy()
    df_sens.loc[mask_imputed, outcome] += delta * df_sens[outcome].std()
    # re-run primary analysis; record HR/OR
    # Plot tipping-point curve: at what δ does conclusion flip?
```

---

## Mode 3 — Full Pipeline

```
Mode 0 (profile) → Mode 1 (missing analysis) → Mode 2 (imputation)
→ Mode 4 (outliers) → Variable coding → Mode 5 (dictionary)
→ Mode 6 (flow diagram numbers) → Mode 7 (audit trail) → Export
```

Output ที่ user จะได้:
1. `data_clean_<date>.xlsx` — analysis-ready dataset
2. `data_dictionary_<date>.xlsx` — codebook (Mode 5)
3. `flow_diagram_numbers_<date>.txt` — CONSORT/STROBE counts
4. `audit_trail_<date>.md` — reproducibility log
5. `missing_pattern.png` — missingness visualization (300 DPI)
6. `methods_data_paragraph_<date>.txt` — paste-ready Methods text (English)

---

## Mode 4 — Outlier Detection

### Continuous variables
```python
def detect_outliers(s, method="iqr", k=1.5):
    if method == "iqr":
        q1, q3 = s.quantile([0.25, 0.75])
        iqr = q3 - q1
        return (s < q1 - k*iqr) | (s > q3 + k*iqr)
    if method == "zscore":
        return (np.abs((s - s.mean())/s.std()) > 3)
    if method == "mad":
        med = s.median()
        mad = (s - med).abs().median()
        return (np.abs((s - med)/(1.4826*mad)) > 3)
```

### Treatment options (pre-specify which)
| Option | When |
|---|---|
| Keep + report | Clinically plausible extreme → primary; sensitivity excluding outliers |
| Winsorize (1st/99th) | Skewed lab values with measurement noise |
| Log-transform | Right-skewed (cytokines, viral load, lab markers) |
| Exclude with justification | Data-entry error confirmed by source verification |

> **Q1 standard**: never silently delete. Every removed observation must appear in CONSORT/STROBE flow with reason.

---

## Mode 5 — Data Dictionary Export

```python
def build_dictionary(df, var_meta):
    """
    var_meta is dict:
      { "age": {"label": "Age at enrollment",
                "type": "continuous", "unit": "years",
                "allowed": "18–120", "derivation": "from DOB and enrol_date"} }
    """
    rows = []
    for col in df.columns:
        meta = var_meta.get(col, {})
        rows.append({
            "variable": col,
            "label": meta.get("label", ""),
            "type": meta.get("type", classify_var(df[col])),
            "unit": meta.get("unit", ""),
            "allowed_values": meta.get("allowed", ""),
            "derivation": meta.get("derivation", "raw"),
            "n_observed": df[col].notna().sum(),
            "n_missing": df[col].isnull().sum(),
            "missing_pct": round(df[col].isnull().mean()*100, 2),
        })
    return pd.DataFrame(rows)

build_dictionary(df, var_meta).to_excel("data_dictionary.xlsx", index=False)
```

> แนบเป็น **Supplementary Table S1** ใน Q1 submission ทุกครั้ง

---

## Mode 6 — Flow Diagram Numbers (CONSORT / STROBE)

### CONSORT (RCT)
```
Assessed for eligibility (n=___)
  ↓ Excluded (n=___)
      • Did not meet inclusion criteria (n=___)
      • Declined to participate (n=___)
      • Other reasons (n=___)
Randomised (n=___)
  ↓
Allocated to intervention (n=___)        Allocated to control (n=___)
  ↓ Lost to follow-up (n=___)              ↓ Lost to follow-up (n=___)
  ↓ Discontinued (n=___)                   ↓ Discontinued (n=___)
Analysed (n=___, ITT)                     Analysed (n=___, ITT)
   Excluded from analysis (n=___)         Excluded from analysis (n=___)
```

### STROBE (cohort/case-control/cross-sectional)
```
Source population (n=___)
  ↓ Did not meet inclusion (n=___)
Eligible (n=___)
  ↓ No outcome data (n=___, excluded)
  ↓ No exposure data (n=___, excluded)
Analytic cohort (n=___)
   Exposed (n=___)   Unexposed (n=___)
```

Skill จะคำนวณตัวเลขเหล่านี้จาก dataset ให้อัตโนมัติและ export เป็น `flow_diagram_numbers.txt`

---

## Mode 7 — Audit Trail

ทุก transformation ถูก log:
```
[2026-05-25 14:32] LOAD  data_raw.xlsx  n=1234, p=87
[2026-05-25 14:33] DROP  duplicates     n=1232 (-2)
[2026-05-25 14:34] EXCLUDE age<18       n=1198 (-34, reason=protocol §2.1)
[2026-05-25 14:35] IMPUTE MICE m=20     vars=[hba1c, ldl, sbp]
[2026-05-25 14:36] DERIVE bmi_cat       from bmi (WHO cutpoints)
[2026-05-25 14:37] LOCK   analysis_v1   sha256=<hash>
```

แนบกับ supplementary material → reviewers พึงพอใจมาก (reproducibility = strong reviewer signal สำหรับ Q1)

---

## Methods Paragraph Template (auto-generated, English)

```
"Data were entered into [REDCap / Excel] and exported as [.csv / .xlsx].
We assessed missingness patterns with Little's MCAR test and logistic
regression of missing indicators on observed covariates. Variables
with ≥5% missing values were imputed using multiple imputation by
chained equations (m = 20 imputations, 10 iterations) implementing
predictive mean matching for skewed continuous variables and
logistic regression for binary variables (van Buuren, 2018). The
primary outcome was [not imputed / imputed with sensitivity analysis].
Sensitivity to a missing-not-at-random departure was evaluated by
delta-adjustment (δ = ±0.5 SD). Outliers, defined as observations
beyond ±3 MAD, were [winsorised at 1st/99th percentile / retained;
sensitivity analyses excluded them]. Estimates were pooled across
imputed datasets using Rubin's rules. The final analysis dataset
was locked on [date] prior to outcome analysis; all transformations
are documented in Supplementary Table S2 (audit trail)."
```

---

## หลักการสำคัญ (Q1 High-IF)

- **อย่า** ลบ observation โดยไม่มี clinical justification ที่ pre-specified
- **อย่า** impute primary outcome โดยไม่ทำ sensitivity analysis
- **อย่า** ใช้ single mean/median imputation ใน Q1 submission (reviewers จะตี)
- **ต้อง** lock analysis dataset ก่อน unblinding (RCT)
- **ต้อง** report missing transparently ใน Table 1 + Methods + CONSORT/STROBE flow
- **ต้อง** ทำ sensitivity analysis (complete-case vs imputed) ทุกครั้ง
- **ต้อง** แนบ data dictionary + audit trail เป็น supplementary material

---

## Reference Files

| File | เนื้อหา |
|---|---|
| `references/missing-data-methods.md` | MICE, PMM, miceforest, Pattern Mixture, tipping-point sensitivity — code เต็ม |
