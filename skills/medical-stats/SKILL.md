---
name: medical-stats
description: >
  ใช้ skill นี้เมื่อผู้ใช้ต้องการ "วิเคราะห์สถิติ", "statistical analysis", "analyze data",
  "วิเคราะห์ผล", "Table 1", "primary outcome", "secondary outcome", "subgroup analysis",
  "ทำ regression", "logistic regression", "linear regression", "multivariable model",
  "survival analysis", "Kaplan-Meier", "Cox regression", "competing risk", "landmark analysis",
  "ROC curve", "AUC", "DeLong test", "sensitivity specificity", "PPV NPV likelihood ratio",
  "calibration", "decision curve analysis", "DCA", "net reclassification index", "NRI", "IDI",
  "propensity score matching", "PSM", "IPTW", "entropy balancing", "ANCOVA", "sample size",
  "power calculation", "interim analysis", "Bayesian analysis", "non-inferiority",
  "วิเคราะห์ก่อนเขียน manuscript", "publish-ready statistics", "Q1 statistics"
metadata:
  version: "1.0.0"
  author: "Chagkrit"
  target_tier: "Q1 high-impact-factor"
---

# Medical Statistical Analysis — Q1/Q2 High-Impact Edition
# ระบบวิเคราะห์สถิติทางการแพทย์ระดับ publication-ready

## บทบาท
ทำหน้าที่เป็น **Senior Clinical Biostatistician** สำหรับงานวิจัยที่จะตีพิมพ์ใน **Q1/Q2 journal ที่มี high impact factor**

ครอบคลุม:
- RCT (parallel, crossover, cluster, factorial, adaptive, non-inferiority)
- Observational (cohort, case-control, cross-sectional, nested case-control)
- Survival (Kaplan-Meier, Cox PH, competing risks, time-varying, landmark, joint models)
- Diagnostic accuracy (ROC, calibration, DCA, NRI/IDI)
- Causal inference (PSM, IPTW, Entropy Balancing, doubly-robust, instrumental variables)
- Prediction models (TRIPOD — derivation, internal/external validation, bootstrap, calibration plot)

ตามมาตรฐาน **CONSORT 2010, STROBE 2007, STARD 2015, TRIPOD 2015, SAMPL (Statistical Analyses and Methods in Published Literature)**

---

## Q1 High-IF Statistical Quality Bar

| ข้อกำหนด | มาตรฐาน |
|---|---|
| Effect estimate | จำเป็น + 95% CI + exact p (3 decimals; "<0.001" ถ้าเล็กกว่า) |
| Multiplicity | ระบุการปรับ (Bonferroni / Holm / Hochberg / pre-specified primary) |
| Pre-specification | analysis plan / SAP จะ lock ก่อน unblinding |
| Assumption checking | normality, equal variance, PH (Cox), linearity (logistic), calibration (prediction) |
| Sensitivity analyses | per protocol vs ITT, complete case vs imputed, with/without outliers, alternative model specifications |
| Reporting | follow SAMPL + ICMJE; ไม่เขียน "NS"; ไม่ใช้ "p = 0.000" |
| Reproducibility | seed-locked code + environment.txt |

---

## Smart Router — เลือก Analysis Track

| Study Design | Track |
|---|---|
| RCT (parallel/crossover/cluster/factorial) | **Track A** |
| Cohort (prospective / retrospective / longitudinal) | **Track B** |
| Case-Control / Cross-sectional | **Track C** |
| Survival / Time-to-event | **Track D** |
| Diagnostic Test Accuracy | **Track E** |
| Causal inference / Group balancing (observational) | **Track F** |
| Prediction model (derivation + validation) | **Track G** |
| Meta-analysis / Systematic review | **Track H** |
| RCT non-inferiority / equivalence | **Track I** |
| Bayesian analysis | **Track J** |

> **Auto-trigger Track F** after Track A/B/C ถ้า Table 1 มี SMD > 0.1 ในตัวแปร baseline หรือ p < 0.05

---

## Phase 0 — Pre-Analysis Checks (ทุก Track)

```python
import pandas as pd
import numpy as np
from scipy import stats
import pingouin as pg

# 1. Normality
def check_normality(s, name):
    s = s.dropna()
    if len(s) < 50:
        stat, p = stats.shapiro(s); test = "Shapiro-Wilk"
    else:
        stat, p = stats.kstest(s, 'norm', args=(s.mean(), s.std())); test = "K-S"
    return {"var": name, "n": len(s), "test": test, "stat": stat, "p": p,
            "normal": p > 0.05}

# 2. Equal variance
def check_variance(g1, g2): return stats.levene(g1, g2)

# 3. Linearity (logistic) — Box-Tidwell
# 4. PH assumption (Cox) — Schoenfeld residuals
# 5. Calibration — calibration plot + Hosmer-Lemeshow / Brier score
# 6. Multicollinearity — VIF (>5 = concern, >10 = problem)
def vif_table(X):
    from statsmodels.stats.outliers_influence import variance_inflation_factor
    return pd.DataFrame({"var": X.columns,
        "VIF": [variance_inflation_factor(X.values, i) for i in range(X.shape[1])]})
```

---

## Sample Size & Power (เสนอก่อนทุก Track)

```python
from statsmodels.stats.power import (TTestIndPower, NormalIndPower,
                                      FTestAnovaPower, GofChisquarePower)
import math

# Continuous (two groups)
TTestIndPower().solve_power(effect_size=0.5, alpha=0.05, power=0.80, ratio=1)

# Proportion (two groups)
NormalIndPower().solve_power(effect_size=0.3, alpha=0.05, power=0.80)

# Survival (Schoenfeld)
def survival_n(HR, p_event, alpha=0.05, power=0.80, ratio=1):
    z_a = stats.norm.ppf(1 - alpha/2); z_b = stats.norm.ppf(power)
    events = ((1+ratio)**2 / ratio) * (z_a + z_b)**2 / (math.log(HR))**2
    n = math.ceil(events / p_event)
    return {"events_needed": math.ceil(events), "total_n": n}

# Diagnostic accuracy (sensitivity at fixed specificity)
def n_diagnostic(target_sens=0.85, ci_width=0.10, prev=0.30):
    z = 1.96
    n_pos = z**2 * target_sens*(1-target_sens) / (ci_width/2)**2
    return math.ceil(n_pos / prev)
```

ทุก protocol ต้องมี sample-size justification — ระบุใน Methods section

---

## Track A — RCT Analysis (CONSORT 2010)

### A1. Table 1 (Baseline + SMD) — Publication-grade

ใช้ `tableone` package (Q1 reviewers ยอมรับ):

```python
from tableone import TableOne

cols = continuous_vars + categorical_vars
categorical = categorical_vars
nonnormal = [v for v in continuous_vars if not check_normality(df[v], v)["normal"]]

t1 = TableOne(df, columns=cols, categorical=categorical,
              groupby='treatment', nonnormal=nonnormal,
              pval=True, smd=True, missing=True, htest_name=True)
t1.to_excel('table1.xlsx')
print(t1.tabulate(tablefmt='grid'))
```

| ข้อกำหนด Q1 | Implementation |
|---|---|
| SMD แทน p-value (RCT) | `smd=True` |
| Median (IQR) สำหรับ non-normal | `nonnormal=[...]` |
| Missing reporting | `missing=True` |
| Test name ทุก row | `htest_name=True` |

> **Q1 reminder**: ใน RCT ไม่ควรเน้น p-value สำหรับ baseline — ใช้ **SMD** (Pocock, 2002)

### A2. Primary outcome analysis (ITT)

```
ITT analysis (primary):
   - All randomised patients regardless of compliance
   - Missing outcomes handled by MICE (m=20, Rubin's rules)
Per-protocol (secondary):
   - Patients who completed protocol as specified
mITT (if pre-specified):
   - At least one dose received + at least one outcome measure

Effect estimate format:
   Continuous: between-group mean difference (95% CI), p
   Binary:     RR, OR, or RD (95% CI), p
   Survival:   HR (95% CI), log-rank p
```

### A3. Subgroup analysis (forest plot, interaction p)

```python
# Pre-specified subgroups only; report interaction p-values
# For RCT: never test more than ~5-7 subgroups
# Always report: estimate (CI) per subgroup + p-interaction
fig, ax = plot_forest_stata(
    estimates=hr_by_subgroup,
    ci_lower=ci_lo,
    ci_upper=ci_hi,
    labels=subgroup_labels,
    null_value=1.0,
    x_label='Hazard Ratio (95% CI)',
    title='Subgroup analysis — Primary endpoint',
    p_interaction=p_interaction_values,
    save_path='fig_subgroup.tiff'
)
```

### A4. Multiplicity control (mandatory in Q1)

| Strategy | When |
|---|---|
| Pre-specified single primary | preferred — no adjustment needed |
| Hierarchical testing | multiple endpoints with priority order |
| Bonferroni | conservative, all-or-none |
| Holm / Hochberg | step-down, more power |
| FDR (Benjamini-Hochberg) | exploratory / many tests |
| Pre-specified gatekeeping | complex confirmatory designs |

ระบุใน Methods + SAP

---

## Track B — Cohort Analysis (STROBE 2007)

### B1. Incidence rate
```python
# IR per 1000 person-years
ir = (events / person_years) * 1000
ci_lo, ci_hi = stats.poisson.interval(0.95, mu=events) / person_years * 1000
```

### B2. Risk Ratio / Hazard Ratio

```python
import statsmodels.api as sm

# Modified Poisson regression with robust SE — preferred for RR in cohort
glm = sm.GLM(df['outcome'], sm.add_constant(df[predictors]),
             family=sm.families.Poisson()).fit(cov_type='HC0')
print(glm.summary())
RR  = np.exp(glm.params)
CI  = np.exp(glm.conf_int())
```

> ใช้ **modified Poisson** สำหรับ RR ใน cohort (Zou 2004) แทน log-binomial (convergence issues)

---

## Track C — Case-Control / Cross-sectional

```python
# Univariable: Fisher exact for 2x2
# Multivariable: logistic with all confounders pre-specified
logit = sm.Logit(df['case'], sm.add_constant(df[predictors])).fit()
OR  = np.exp(logit.params)
CI  = np.exp(logit.conf_int())

# Cross-sectional: prevalence ratio via modified Poisson
```

> **Q1 standard**: separate **univariable + multivariable** results in tables; show estimate (95% CI) for both

---

## Track D — Survival Analysis (STATA-style figures)

```python
from lifelines import KaplanMeierFitter, CoxPHFitter
from lifelines.statistics import logrank_test, proportional_hazard_test
import numpy as np

# ─── KM curve (mandatory at-risk table for Q1) ───
fig, ax = plot_km_stata(
    df=df, time_col='time', event_col='event', group_col='group',
    group_labels={0:'Control', 1:'Treatment'}, time_unit='months',
    title='Overall Survival', ylabel='Survival probability',
    show_at_risk=True, show_ci=True, show_censoring=True,
    save_path='fig1_km.tiff'   # ≥300 DPI
)

# ─── Cox PH ───
cph = CoxPHFitter()
cph.fit(df[['time','event'] + covariates],
        duration_col='time', event_col='event')
cph.print_summary()

# ─── PH assumption test (mandatory for Q1) ───
ph_test = proportional_hazard_test(cph, df, time_transform='rank')
print(ph_test.summary)   # p > 0.05 = PH holds

# If PH violated:
#   - stratify by violating covariate, or
#   - include time-varying coefficient, or
#   - use Royston-Parmar flexible parametric model

# ─── Competing risks (Fine-Gray) ───
# from lifelines import CRCSplineFitter / use cmprsk in R via rpy2 if needed
# ─── Landmark analysis ───
# Restrict to patients alive at t* and re-estimate
```

ดู `references/survival-analysis.md` สำหรับ competing risks, joint models, time-varying covariates

---

## Track E — Diagnostic Accuracy (STARD 2015)

```python
from sklearn.metrics import roc_auc_score, roc_curve, confusion_matrix

# ─── ROC curve (mandatory CI band, Youden, AUC inset for Q1) ───
fig, ax = plot_roc_stata(
    y_true=y, y_scores=[model1_score, model2_score],
    model_labels=['Clinical score', 'Biomarker model'],
    show_optimal=True, show_diagonal=True, ci_method='bootstrap',
    title='ROC comparison', save_path='fig_roc.tiff'
)

# ─── DeLong test (compare two AUCs) ───
# Use `delong` python port or rpy2 calling pROC::roc.test
# OR bootstrap:
def delong_test(y, score_a, score_b, n_boot=2000, seed=42):
    rng = np.random.default_rng(seed)
    diffs = []
    for _ in range(n_boot):
        idx = rng.integers(0, len(y), len(y))
        a = roc_auc_score(y[idx], score_a[idx])
        b = roc_auc_score(y[idx], score_b[idx])
        diffs.append(a - b)
    diffs = np.array(diffs)
    p = 2 * min((diffs <= 0).mean(), (diffs >= 0).mean())
    return diffs.mean(), np.percentile(diffs, [2.5, 97.5]), p

# ─── Calibration (mandatory for Q1 prediction models) ───
from sklearn.calibration import calibration_curve
prob_true, prob_pred = calibration_curve(y, prob, n_bins=10, strategy='quantile')
# Hosmer-Lemeshow chi-square
# Brier score
from sklearn.metrics import brier_score_loss
brier = brier_score_loss(y, prob)

# ─── Decision Curve Analysis (mandatory for Q1 prediction) ───
def decision_curve(y_true, y_prob, thresholds=np.linspace(0.01, 0.99, 99)):
    n = len(y_true); prev = y_true.mean()
    nb_model, nb_all, nb_none = [], [], []
    for t in thresholds:
        tp = ((y_prob >= t) & (y_true == 1)).sum()
        fp = ((y_prob >= t) & (y_true == 0)).sum()
        nb_model.append(tp/n - fp/n * (t/(1-t)))
        nb_all.append(prev - (1-prev)*(t/(1-t)))
        nb_none.append(0)
    return pd.DataFrame({"threshold": thresholds, "Model": nb_model,
                         "Treat all": nb_all, "Treat none": nb_none})

# Plot net benefit vs threshold probability — STATA style
```

> **Q1 diagnostic mandatory**: ROC + 95% CI (DeLong or bootstrap) + calibration + DCA + reclassification (NRI/IDI when comparing models)

---

## Track F — Causal Inference / Group Balancing

ดูรายละเอียดเต็มใน `references/group-balancing.md`

### F1. Method matrix

| Method | When | Notes |
|---|---|---|
| **PSM** (1:1 NN with caliper) | n moderate, few confounders | Reduces sample; balance on PS |
| **IPTW** | preserve full sample | Use stabilized weights; trim extreme weights |
| **Entropy Balancing** | many confounders, exact moment balance | Hainmueller 2012 |
| **CEM** (Coarsened Exact Match) | many covariates, prefer exact | Reduces variance, may drop |
| **ANCOVA / regression adjustment** | RCT with minor imbalance | Pre-specified covariates |
| **Doubly-robust** (TMLE / AIPW) | maximum protection | requires both outcome + treatment models |
| **Instrumental variable** | unmeasured confounding | strong assumptions; report falsification tests |

### F2. Balance diagnostics (mandatory before/after)

```python
# Love plot — STATA style
fig, ax = plot_love_stata(
    smd_before=before, smd_after=after, var_names=confounders,
    threshold=0.1, save_path='fig_love.tiff'
)
# Q1 standard: ALL SMD < 0.1 after balancing
```

### F3. Reporting (paste-ready)
```
"We estimated the average treatment effect using inverse probability
of treatment weighting (IPTW) with stabilised weights derived from a
logistic regression of treatment on baseline covariates [list].
Standardised mean differences (SMD) were used to assess balance,
with SMD < 0.1 considered adequate (Austin, 2009). Weighted Cox
regression with robust variance was used for the outcome model.
E-value was calculated to quantify sensitivity to unmeasured
confounding (VanderWeele & Ding, 2017)."
```

### F4. E-value (Q1 expects this for observational causal inference)

```python
def e_value(rr_or_hr, rr_ci_lower=None):
    """VanderWeele & Ding 2017"""
    e = rr_or_hr + np.sqrt(rr_or_hr * (rr_or_hr - 1))
    if rr_ci_lower is not None and rr_ci_lower > 1:
        e_ci = rr_ci_lower + np.sqrt(rr_ci_lower * (rr_ci_lower - 1))
    else:
        e_ci = 1.0
    return {"E_estimate": e, "E_CI": e_ci}
```

---

## Track G — Prediction Model (TRIPOD 2015)

```
Design:
  1. Derivation cohort → fit model
  2. Internal validation → bootstrap (B=1000) for optimism correction
  3. External validation → independent cohort (Q1 expects this if possible)

Reporting (mandatory):
  - Discrimination: AUC + 95% CI
  - Calibration: intercept, slope, calibration plot
  - Clinical utility: Decision Curve Analysis
  - Reclassification (vs existing model): NRI, IDI
  - Model presentation: equation, nomogram, or web calculator
  - Sensitivity: by subgroup
```

```python
# Bootstrap internal validation with optimism correction
def bootstrap_optimism(X, y, fit_fn, B=1000, seed=42):
    rng = np.random.default_rng(seed)
    apparent = fit_fn(X, y).score(X, y)
    optimism = []
    for _ in range(B):
        idx = rng.integers(0, len(y), len(y))
        m = fit_fn(X.iloc[idx], y.iloc[idx])
        boot_perf = m.score(X.iloc[idx], y.iloc[idx])
        original_perf = m.score(X, y)
        optimism.append(boot_perf - original_perf)
    return apparent - np.mean(optimism)
```

---

## Track H — Meta-analysis / Systematic Review (PRISMA 2020)

```python
# Random-effects DerSimonian-Laird or Paule-Mandel
# I² heterogeneity, τ², 95% prediction interval
# Funnel plot + Egger's test
# Trim-and-fill / PET-PEESE for publication bias

# Use `pymeta` or call R `metafor` via rpy2 for Q1-grade outputs
```

PRISMA flow diagram numbers + risk-of-bias (RoB 2 / ROBINS-I) table mandatory

---

## Track I — Non-inferiority / Equivalence RCT

```
Pre-specify:
  - Non-inferiority margin (Δ) with clinical justification
  - One-sided 97.5% CI (= two-sided 95% lower bound)
  - Both ITT and PP analyses (CONSORT-NI 2012)

Reporting:
  "The 95% CI for the difference (active − control) was [lo, hi].
   The lower bound exceeded the pre-specified non-inferiority margin
   of −Δ, supporting non-inferiority."
```

---

## Track J — Bayesian Analysis

```python
# Use PyMC or call brms via rpy2
import pymc as pm

with pm.Model() as m:
    beta = pm.Normal('beta', 0, 2.5)
    p = pm.invlogit(beta + intercept)
    y_obs = pm.Bernoulli('y', p=p, observed=y)
    idata = pm.sample(4000, tune=2000, chains=4, target_accept=0.95)

# Report: posterior median + 95% credible interval + P(effect > 0)
# Prior sensitivity analysis (vague vs informed)
# Convergence: R-hat < 1.01, ESS > 1000
```

---

## Figure Standards — STATA Style (BANGCAB for Q1)

ดูรายละเอียดใน `../manuscript-writer/references/stata-figure-style.md`

| Figure | Function | Notes |
|---|---|---|
| KM curve | `plot_km_stata()` | at-risk table + CI band + censoring marks |
| ROC | `plot_roc_stata()` | CI band + Youden + AUC inset |
| Forest | `plot_forest_stata()` | diamond + subgroup interaction p |
| Calibration | `plot_calibration_stata()` | observed vs predicted + density rug |
| DCA | `plot_dca_stata()` | net benefit vs threshold |
| Love plot | `plot_love_stata()` | SMD before/after balancing |
| Funnel | `plot_funnel_stata()` | for meta-analysis |
| Bland-Altman | `plot_ba_stata()` | mean ± 1.96 SD limits |

> **Q1 mandatory**: ≥300 DPI, TIFF or PNG, label QC auto-run, color-blind safe palette

---

## Statistical Reporting — Q1 Format

```
Continuous (normal):     mean ± SD
Continuous (non-normal): median (IQR) or median (Q1–Q3)
Categorical:             n (%)
Effect (binary):         OR / RR / RD (95% CI)
Effect (continuous):     mean difference (95% CI)
Survival:                HR (95% CI); median (95% CI) time
Diagnostic:              AUC 0.XX (95% CI X.XX–X.XX); Sn XX%, Sp XX%
P-value:                 exact to 3 decimals; <0.001 if smaller
                         NEVER write "NS"; NEVER write "p = 0.000"
Sample size:             always include in every table column header (n=___)
Missing:                 report n analysed when different from n randomised
```

---

## Sensitivity Analyses (mandatory for Q1)

Pre-specify ≥3 of the following:
1. ITT vs PP vs mITT (RCT)
2. Complete-case vs MICE imputed
3. With vs without outliers
4. Alternative model specification (e.g., parametric vs non-parametric)
5. Different cutpoint for continuous predictors
6. Subgroup analysis (limited, pre-specified)
7. E-value for unmeasured confounding (observational)
8. Different missing-data assumption (MAR vs MNAR delta)
9. Restricted to high-quality data subset
10. Time-period restriction (recency)

แสดงผลในรูป tornado plot หรือ sensitivity table

---

## Phase Final — Results Package Export

Auto-export:
1. `table1.xlsx` — baseline characteristics
2. `table2_outcomes.xlsx` — primary + secondary outcomes
3. `table3_multivariable.xlsx` — adjusted models
4. `fig1_<analysis>.tiff` … (≥300 DPI)
5. `results_paragraph.txt` — paste-ready English Results text
6. `statistical_methods.txt` — paste-ready English Methods text
7. `sap_summary.md` — Statistical Analysis Plan summary
8. `sensitivity_report.xlsx` — all sensitivity analyses

---

## หลักการสำคัญ (Q1 High-IF)

- **อย่า** ทำ post-hoc analysis โดยไม่ระบุว่าเป็น exploratory
- **อย่า** ใช้ p-value เป็นเกณฑ์เดียวในการตัดสิน clinical significance
- **อย่า** dichotomize continuous variables โดยไม่มีเหตุผล (loss of power; Royston 2006)
- **อย่า** report only adjusted effect — แสดง unadjusted ด้วยเสมอ
- **ต้อง** ระบุ multiplicity adjustment เมื่อมี ≥2 endpoints
- **ต้อง** ทำ assumption checking ก่อนใช้ parametric test
- **ต้อง** ทำ sensitivity analyses (≥3)
- **ต้อง** ระบุ effect size + 95% CI ทุกผล (ไม่ใช่แค่ p)
- **ต้อง** ใช้ STATA-style figures ≥300 DPI

---

## Reference Files

| File | เนื้อหา |
|---|---|
| `references/inferential-stats.md` | t-test, ANOVA, regression, non-parametric — code + interpretation |
| `references/survival-analysis.md` | KM, Cox, competing risks, landmark, joint models, time-varying |
| `references/diagnostic-accuracy.md` | ROC, DeLong, calibration, DCA, NRI/IDI, IDI |
| `references/group-balancing.md` | PSM, IPTW, Entropy, CEM, ANCOVA, doubly-robust, E-value |
| `../manuscript-writer/references/stata-figure-style.md` | STATA-style theme + all plot functions |
