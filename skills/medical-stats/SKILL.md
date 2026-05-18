---
name: medical-stats
description: >
  ใช้ skill นี้เมื่อผู้ใช้ต้องการ "วิเคราะห์สถิติ", "statistical analysis", "analyze data",
  "วิเคราะห์ผล", "คำนวณ p-value", "ทำ regression", "survival analysis", "Kaplan-Meier",
  "Cox regression", "ROC curve", "AUC", "sensitivity specificity", "Table 1", "descriptive statistics",
  "inferential statistics", "sample size calculation", "วิเคราะห์ข้อมูลวิจัย", "run statistics",
  "multivariate analysis", "logistic regression", หรือ "วิเคราะห์ก่อนเขียน manuscript"
metadata:
  version: "0.1.0"
  author: "Chagkrit"
---

# Medical Statistical Analysis
# ระบบวิเคราะห์สถิติทางการแพทย์ครบวงจร

## บทบาท
ทำหน้าที่เป็น **Clinical Biostatistician** ผู้เชี่ยวชาญการวิเคราะห์สถิติทางการแพทย์ ครอบคลุม RCT, Cohort, Case-Control, Cross-sectional, Survival Analysis และ Diagnostic Accuracy ตามมาตรฐาน CONSORT / STROBE / STARD / TRIPOD

---

## Smart Router — เลือก Analysis Type

ถามผู้ใช้หากยังไม่ชัดเจน:

| Study Design | Analysis Track |
|---|---|
| RCT | **Track A** — RCT Analysis |
| Cohort / Longitudinal | **Track B** — Cohort Analysis |
| Case-Control / Cross-sectional | **Track C** — Observational Analysis |
| Survival / Time-to-event | **Track D** — Survival Analysis |
| Diagnostic Test Accuracy | **Track E** — Diagnostic Accuracy |
| สองกลุ่มมี baseline characteristics แตกต่างกัน (SMD > 0.1 หรือ p < 0.05 ใน Table 1) | **Track F** — Group Balancing |
| "วิเคราะห์ทุกอย่าง" / ไม่แน่ใจ | → ถามรายละเอียด study design ก่อน |

> **Auto-trigger Track F**: หลังสร้าง Table 1 ถ้าพบ SMD > 0.1 ในตัวแปรใดก็ตาม หรือมี p < 0.05 ในตัวแปร baseline → แจ้งผู้ใช้ทันทีและเสนอให้ทำ Group Balancing ก่อนวิเคราะห์ผลลัพธ์หลัก

---

## Phase 0 — Assumption Checking (ทุก Track)

ก่อนวิเคราะห์ ตรวจสอบ assumptions เสมอ:

```python
import pandas as pd
import numpy as np
from scipy import stats
import pingouin as pg

# 1. Normality test
def check_normality(data, var_name):
    n = len(data.dropna())
    if n < 50:
        stat, p = stats.shapiro(data.dropna())
        test_name = "Shapiro-Wilk"
    else:
        stat, p = stats.kstest(data.dropna(), 'norm',
                               args=(data.mean(), data.std()))
        test_name = "Kolmogorov-Smirnov"

    normal = "✅ Normal" if p > 0.05 else "❌ Non-normal"
    print(f"{var_name}: {test_name} W={stat:.3f}, p={p:.4f} → {normal}")
    return p > 0.05

# 2. Equal variance (Levene's test)
def check_variance(group1, group2):
    stat, p = stats.levene(group1, group2)
    equal_var = "✅ Equal variance" if p > 0.05 else "❌ Unequal variance"
    print(f"Levene's test: F={stat:.3f}, p={p:.4f} → {equal_var}")
    return p > 0.05
```

---

## Track A — RCT Analysis

### A1. Generate Table 1 (Baseline Characteristics + SMD Detection)

```python
def compute_smd(group1, group2, is_continuous=True):
    """
    คำนวณ Standardized Mean Difference (SMD) — Cohen's d สำหรับ continuous,
    หรือ SMD สำหรับ binary/categorical
    SMD < 0.1  = balanced  ✅
    SMD 0.1–0.2 = borderline ⚠️
    SMD > 0.2  = imbalanced ❌
    """
    if is_continuous:
        pooled_sd = np.sqrt((group1.std()**2 + group2.std()**2) / 2)
        if pooled_sd == 0:
            return 0.0
        return abs(group1.mean() - group2.mean()) / pooled_sd
    else:
        # Binary: SMD = (p1 - p2) / sqrt((p1*(1-p1) + p2*(1-p2)) / 2)
        p1, p2 = group1.mean(), group2.mean()
        denom = np.sqrt((p1*(1-p1) + p2*(1-p2)) / 2)
        if denom == 0:
            return 0.0
        return abs(p1 - p2) / denom

def generate_table1(df, group_col, continuous_vars, categorical_vars):
    """
    สร้าง Table 1 มาตรฐาน พร้อม p-values และ SMD
    พร้อม auto-detect imbalance เพื่อ trigger Track F
    """
    results = []
    imbalanced_vars = []  # เก็บตัวแปรที่ SMD > 0.1

    groups = df[group_col].unique()
    group_names = [f"Group {g}" for g in groups]

    for var in continuous_vars:
        # ตรวจ normality
        is_normal = check_normality(df[var], var)

        group_data = [df[df[group_col] == g][var].dropna() for g in groups]

        if is_normal:
            # t-test หรือ ANOVA
            if len(groups) == 2:
                stat, p = stats.ttest_ind(*group_data)
                test = "t-test"
            else:
                stat, p = stats.f_oneway(*group_data)
                test = "ANOVA"
            summary = [f"{g.mean():.2f} ± {g.std():.2f}" for g in group_data]
        else:
            # Mann-Whitney หรือ Kruskal-Wallis
            if len(groups) == 2:
                stat, p = stats.mannwhitneyu(*group_data)
                test = "Mann-Whitney"
            else:
                stat, p = stats.kruskal(*group_data)
                test = "Kruskal-Wallis"
            summary = [f"{g.median():.2f} ({g.quantile(0.25):.2f}–{g.quantile(0.75):.2f})"
                      for g in group_data]

        # คำนวณ SMD (เฉพาะ 2 กลุ่ม)
        smd_str = ""
        if len(groups) == 2:
            smd = compute_smd(group_data[0], group_data[1], is_continuous=True)
            smd_flag = "✅" if smd < 0.1 else ("⚠️" if smd < 0.2 else "❌")
            smd_str = f"{smd:.3f} {smd_flag}"
            if smd > 0.1:
                imbalanced_vars.append(var)

        results.append({
            "Variable": var, **dict(zip(group_names, summary)),
            "p-value": f"{p:.3f}", "SMD": smd_str, "Test": test
        })

    for var in categorical_vars:
        ct = pd.crosstab(df[group_col], df[var])
        stat, p, dof, expected = stats.chi2_contingency(ct)

        # ถ้า expected < 5 ใน > 20% cells → Fisher's exact
        if (expected < 5).sum() / expected.size > 0.2 and ct.shape == (2, 2):
            _, p = stats.fisher_exact(ct)
            test = "Fisher's exact"
        else:
            test = "Chi-square"

        for category in df[var].unique():
            counts = [df[(df[group_col] == g) & (df[var] == category)].shape[0]
                     for g in groups]
            totals = [df[df[group_col] == g].shape[0] for g in groups]
            summary = [f"{c} ({c/t*100:.1f}%)" for c, t in zip(counts, totals)]

            # SMD สำหรับ binary (เฉพาะ 2 กลุ่ม)
            smd_str = ""
            if len(groups) == 2 and category == df[var].unique()[0]:
                bin1 = (df[df[group_col] == groups[0]][var] == category).astype(float)
                bin2 = (df[df[group_col] == groups[1]][var] == category).astype(float)
                smd = compute_smd(bin1, bin2, is_continuous=False)
                smd_flag = "✅" if smd < 0.1 else ("⚠️" if smd < 0.2 else "❌")
                smd_str = f"{smd:.3f} {smd_flag}"
                if smd > 0.1:
                    imbalanced_vars.append(var)

            results.append({
                "Variable": f"  {var}: {category}",
                **dict(zip(group_names, summary)),
                "p-value": f"{p:.3f}" if category == df[var].unique()[0] else "",
                "SMD": smd_str,
                "Test": test if category == df[var].unique()[0] else ""
            })

    table1_df = pd.DataFrame(results)

    # ─── Auto-detect imbalance ───────────────────────────────────────────
    if imbalanced_vars and len(groups) == 2:
        print("\n" + "="*60)
        print("⚠️  IMBALANCE DETECTED — Track F: Group Balancing แนะนำ")
        print("="*60)
        print(f"ตัวแปรที่มี SMD > 0.1: {', '.join(imbalanced_vars)}")
        print("ควรทำ Group Balancing ก่อนวิเคราะห์ผลลัพธ์หลัก")
        print("→ ดูรายละเอียดใน references/group-balancing.md")
        print("="*60 + "\n")

    return table1_df, imbalanced_vars
```

> **หมายเหตุ**: ฟังก์ชันนี้คืนค่า tuple `(table1_df, imbalanced_vars)` — ถ้า `imbalanced_vars` ไม่ว่างเปล่า ให้ดำเนินการ **Track F** ต่อทันที

### A2. Primary Outcome Analysis
ดูรายละเอียดใน `references/inferential-stats.md`

---

## Track B — Cohort Analysis

### B1. Incidence Rate
```python
# Incidence rate = events / person-time
incidence_rate = events / person_years * 1000  # per 1000 person-years

# Rate ratio
rate_ratio = ir_exposed / ir_unexposed
```

### B2. Risk Ratio / Hazard Ratio
```python
import statsmodels.api as sm

# Logistic regression สำหรับ binary outcome
X = sm.add_constant(df[predictors])
model = sm.Logit(df['outcome'], X)
result = model.fit()
print(result.summary())

# Odds Ratio + 95% CI
OR = np.exp(result.params)
CI = np.exp(result.conf_int())
```

---

## Track C — Case-Control / Cross-sectional

### C1. Odds Ratio Calculation
```python
# Univariable
for var in independent_vars:
    ct = pd.crosstab(df['case'], df[var])
    OR, p = stats.fisher_exact(ct)

# Multivariable logistic regression
X = sm.add_constant(df[predictors])
logit_model = sm.Logit(df['outcome'], X).fit()
OR = np.exp(logit_model.params)
CI_lower = np.exp(logit_model.conf_int()[0])
CI_upper = np.exp(logit_model.conf_int()[1])
```

### C2. Effect Size
```python
import pingouin as pg

# Cramér's V (categorical)
pg.chi2_independence(df, x='exposure', y='outcome', correction=False)

# Cohen's d (continuous)
pg.compute_effsize(group1, group2, eftype='cohen')
```

---

## Track D — Survival Analysis

> **Figure Style**: ใช้ `plot_km_stata()` จาก `references/stata-figure-style.md` เสมอ

```python
from lifelines import KaplanMeierFitter, CoxPHFitter
from lifelines.statistics import logrank_test, multivariate_logrank_test

# ─── Kaplan-Meier (STATA style) ───────────────────────────────────────
# โหลด STATA style functions จาก stata-figure-style.md
# แล้วเรียกใช้:

fig, ax = plot_km_stata(
    df=df,
    time_col='time',          # ชื่อ column เวลา
    event_col='event',        # ชื่อ column event (1=event, 0=censor)
    group_col='group',        # ชื่อ column กลุ่ม
    group_labels={0: 'Control', 1: 'Treatment'},
    time_unit='months',
    title='Overall Survival by Treatment Group',
    ylabel='Survival Probability',
    show_at_risk=True,        # แสดง at-risk table ด้านล่าง
    save_path='figure1_km.tiff'
)

# ─── Cox Proportional Hazards ──────────────────────────────────────────
cph = CoxPHFitter()
cph.fit(df[['time', 'event'] + covariates],
        duration_col='time', event_col='event')
cph.print_summary()

# ─── Forest plot จาก Cox results (STATA style) ────────────────────────
# ดึง HR และ 95% CI จาก Cox model
hr = np.exp(cph.params_)
ci_lower = np.exp(cph.confidence_intervals_.iloc[:, 0])
ci_upper = np.exp(cph.confidence_intervals_.iloc[:, 1])

fig, ax = plot_forest_stata(
    estimates=hr.values,
    ci_lower=ci_lower.values,
    ci_upper=ci_upper.values,
    labels=hr.index.tolist(),
    null_value=1.0,
    x_label='Hazard Ratio (95% CI)',
    title='Cox Regression — Multivariable Analysis',
    save_path='figure2_forest.tiff'
)

# Check PH assumption
cph.check_assumptions(df, show_plots=True)
```

ดูรายละเอียดใน `references/survival-analysis.md`

---

## Track E — Diagnostic Accuracy

> **Figure Style**: ใช้ `plot_roc_stata()` จาก `references/stata-figure-style.md` เสมอ

```python
from sklearn.metrics import (roc_auc_score, roc_curve,
                              confusion_matrix, classification_report)
import numpy as np

# ─── ROC Curve (STATA style) ──────────────────────────────────────────
# Single model:
fig, ax = plot_roc_stata(
    y_true=y_true,
    y_scores=y_score,
    model_labels=['Prediction Model'],
    title='ROC Curve for Diagnostic Model',
    save_path='figure_roc.tiff'
)

# Multiple models (comparison):
fig, ax = plot_roc_stata(
    y_true=y_true,
    y_scores=[y_score_model1, y_score_model2],
    model_labels=['Clinical Score', 'Biomarker Model'],
    title='ROC Curve Comparison',
    save_path='figure_roc_comparison.tiff'
)

# ─── Diagnostic Metrics ───────────────────────────────────────────────
fpr, tpr, thresholds = roc_curve(y_true, y_score)
auc = roc_auc_score(y_true, y_score)

# Youden's Index (optimal cutpoint)
youden_index = tpr - fpr
optimal_idx = np.argmax(youden_index)
optimal_threshold = thresholds[optimal_idx]

# Confusion matrix at optimal threshold
y_pred = (y_score >= optimal_threshold).astype(int)
tn, fp, fn, tp = confusion_matrix(y_true, y_pred).ravel()

# Metrics
sensitivity = tp / (tp + fn)
specificity = tn / (tn + fp)
ppv = tp / (tp + fp)  # Positive Predictive Value
npv = tn / (tn + fn)  # Negative Predictive Value
plr = sensitivity / (1 - specificity)  # Positive Likelihood Ratio
nlr = (1 - sensitivity) / specificity  # Negative Likelihood Ratio
dor = plr / nlr  # Diagnostic Odds Ratio

# 95% CI สำหรับ AUC (DeLong method)
from scipy import stats
n1 = sum(y_true == 1)
n0 = sum(y_true == 0)
q1 = auc / (2 - auc)
q2 = 2 * auc**2 / (1 + auc)
se_auc = np.sqrt((auc*(1-auc) + (n1-1)*(q1-auc**2) + (n0-1)*(q2-auc**2)) / (n1*n0))
auc_ci_lower = max(0, auc - 1.96*se_auc)
auc_ci_upper = min(1, auc + 1.96*se_auc)
print(f"AUC = {auc:.3f} (95% CI {auc_ci_lower:.3f}–{auc_ci_upper:.3f})")
```

ดูรายละเอียดและ subgroup analysis ใน `references/diagnostic-accuracy.md`

---

## Track F — Group Balancing (สองกลุ่มที่มี Baseline Imbalance)

> **Trigger**: ใช้ Track นี้เมื่อ Table 1 แสดง SMD > 0.1 หรือ p < 0.05 ในตัวแปร baseline สำหรับ **observational studies** (Cohort, Case-Control, Retrospective) — ดูรายละเอียดเต็มใน `references/group-balancing.md`

### F0. Method Selection Router

```
ข้อมูลเป็น observational study + 2 กลุ่มมี baseline แตกต่างกัน
│
├── Sample size เล็ก (n < 500 ต่อกลุ่ม)?
│   ├── YES → Propensity Score Matching (PSM) ✅ แนะนำอันดับ 1
│   └── NO  → IPTW หรือ Entropy Balancing
│
├── มี many confounders (> 10 ตัวแปร)?
│   ├── YES → Entropy Balancing (ดีกว่า PSM เมื่อ confounders เยอะ)
│   └── NO  → PSM หรือ IPTW
│
├── ต้องการ preserve sample size ทั้งหมด?
│   ├── YES → IPTW หรือ Entropy Balancing (ไม่ทิ้ง observation)
│   └── NO  → PSM (ทิ้ง unmatched cases)
│
├── RCT ที่มี baseline imbalance เล็กน้อย?
│   └── → ANCOVA (covariate adjustment) — ไม่จำเป็นต้อง match
│
└── Case-Control retrospective?
    └── → Matched Case-Control หรือ PSM
```

### F1. Quick Start — Propensity Score Matching (PSM)

```python
# สำหรับรายละเอียดเต็มและ code ทั้งหมด → references/group-balancing.md
from causalml.match import NearestNeighborMatch
# หรือใช้ sklearn + custom matching

# Step 1: Estimate propensity score
from sklearn.linear_model import LogisticRegression

X = df[confounders]
y = df[treatment_col]  # 1 = treatment, 0 = control

ps_model = LogisticRegression(max_iter=1000)
ps_model.fit(X, y)
df['propensity_score'] = ps_model.predict_proba(X)[:, 1]

# Step 2: 1:1 Nearest Neighbor Matching
# → ดู references/group-balancing.md สำหรับ code เต็ม

# Step 3: ตรวจสอบ balance หลัง matching ด้วย SMD + Love Plot
# → SMD < 0.1 ทุกตัวแปร = balanced ✅
```

### F2. ตรวจสอบ Balance หลัง Matching/Weighting

```python
# ตรวจ SMD ก่อนและหลัง — ต้องดีขึ้น
before_smd = {var: compute_smd(...) for var in confounders}
after_smd  = {var: compute_smd(...) for var in confounders}  # ใช้ matched/weighted data

# Love Plot (STATA style)
# → ดู references/group-balancing.md สำหรับ plot_love_stata()
```

### F3. วิเคราะห์ Outcome หลัง Balancing

```python
# หลัง balance แล้ว → วิเคราะห์ outcome ด้วยวิธีเดิม (Track A–E)
# แต่ใช้ matched dataset หรือ weighted analysis แทน original data

# PSM: ใช้ df_matched แทน df
# IPTW: ใส่ weights= parameter ใน statsmodels
```

> **Reporting**: ต้องรายงานทั้ง before และ after balance (SMD table + Love Plot) ใน manuscript — ดูตัวอย่างใน `references/group-balancing.md`

ดูรายละเอียดเต็ม code สำหรับ PSM, IPTW, Entropy Balancing, CEM และ ANCOVA ใน `references/group-balancing.md`

---

## Phase Final — Results Reporting

สร้าง output สำหรับ manuscript:

1. **Statistical tables** ในรูปแบบ publication-ready (exportable เป็น Excel/Word)
2. **Figures** (Kaplan-Meier curve, ROC curve, Forest plot) พร้อม axis labels
3. **Results text template** สำหรับนำไปเขียน manuscript

### Statistical Reporting Format
```
Continuous: mean ± SD หรือ median (IQR) ตาม distribution
Categorical: n (%)
Hypothesis test: statistic (df), p-value (2-tailed)
Effect size: OR/RR/HR (95% CI)
Survival: median OS [95% CI] months
Diagnostic: AUC 0.XX (95% CI 0.XX–0.XX), Sensitivity XX%, Specificity XX%
P-value: exact values ถึง 3 decimal places (ไม่เขียน NS)
          p < 0.001 ถ้า < 0.001
```

## Reference Files
| File | เนื้อหา |
|---|---|
| `references/inferential-stats.md` | t-test, ANOVA, Chi-square, regression — code และ interpretation |
| `references/survival-analysis.md` | KM, Cox PH, competing risks, landmark analysis |
| `references/diagnostic-accuracy.md` | ROC, subgroup, calibration, DCA |
| `references/group-balancing.md` | **PSM, IPTW, Entropy Balancing, CEM, ANCOVA** — code เต็ม + Love Plot + reporting template |
| `../manuscript-writer/references/stata-figure-style.md` | STATA-style Python theme — ใช้สำหรับ figure ทุกประเภท |
