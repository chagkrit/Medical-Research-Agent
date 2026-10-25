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

## STATA 18 Requirement (MANDATORY)

> **ทุก analysis ในระบบนี้ต้องใช้ STATA 18 เท่านั้น**
> ห้ามใช้ Python, R, SAS หรือซอฟต์แวร์อื่น เว้นแต่ผู้ใช้ระบุเป็นอื่น

```stata
version 18
set more off
set scheme s2color       // STATA standard color scheme สำหรับ publication
log using "analysis.log", replace text
```

ทุก do-file ต้องเริ่มด้วย 3 บรรทัดข้างต้น และปิดด้วย `log close`

Deliverables ต่อการวิเคราะห์:
- `analysis_main.do` — do-file หลัก (reproducible)
- `analysis.log` — log file ครบถ้วน
- `table1.xlsx`, `table2_outcomes.xlsx`, `table3_multivariable.xlsx` — ผ่าน `putexcel`
- `fig*.tif` — ≥300 DPI ทุก figure

---

## Verification Discipline — ทุกตัวเลขต้อง trace กลับ source ก่อนเข้า manuscript

**กฎ:** ทุก figure ใน manuscript → 1 แถวใน results ledger → 1 บรรทัด logged STATA output → reconcile กับ source data. ขาด link ไหน = ตัวเลขนั้นเป็นแค่ hypothesis

1. **Data integrity first** — reconcile event counts / N ต่อ arm / follow-up กับ **primary source (PubMed / paper ต้นฉบับ)** *ก่อน* เขียน do-file. Back-calculated numbers ผิดบ่อยกว่าถูก — เป็น verification ที่ให้ค่าสูงสุดในทั้ง pipeline
2. **รัน STATA 18 → เก็บ log → อ่าน log** — do-file รันผ่าน ≠ estimate ถูก. อ่าน output จริง ยืนยัน model, N, events, CI
3. **One results ledger = single source of truth** — สร้าง `results_ledger.csv` (1 แถว/ตัวเลข). manuscript ถูกเช็ก *เทียบกับ* ledger ไม่ใช่กลับกัน. ตอน write / QC ให้ **sweep ทุกที่ที่ตัวเลขปรากฏ** (Abstract = Results = Table = figure caption) ให้ตรง ledger เป๊ะ — ตัวเลขที่ drift คือสิ่งที่ reviewer จับ (การ sweep นี้ fire ต่อใน `peer-review` / `manuscript-writer` ตอน write-time)
4. **เช็ก "ชนิด" ของ estimate ไม่ใช่แค่ค่า** — รายงาน RR ตอนที่ text บอก OR? CI อยู่ log หรือ natural scale? near-ceiling proportion → RD ไม่ใช่ OR (`metan nointeger or` ให้ log-RR ไม่ใช่ OR)
5. **Verify ด้วยตาใหม่** — การอ่านทวน ledger ของตัวเองใช้ blind spot เดียวกัน. อะไรที่ material ให้ delegate การ recompute *อิสระ* (subagent) reconcile counts กับ source ใหม่โดยไม่เห็น annotation ของเรา
6. **Re-diagnose อย่า re-run** — ตัวเลข reconcile ไม่ได้หลังลองอย่างซื่อสัตย์ 2 ครั้ง = data entry / source extraction ผิด ไม่ใช่ do-file. Reconcile จาก source ใหม่ อย่าจูน do-file ไปเรื่อย

> ใช้ template ledger + sweep script ที่ `Research/_templates/results-consistency-ledger/`

---

## บทบาท
ทำหน้าที่เป็น **Senior Clinical Biostatistician** สำหรับงานวิจัยที่จะตีพิมพ์ใน **Q1/Q2 journal ที่มี high impact factor**

ครอบคลุม:
- RCT (parallel, crossover, cluster, factorial, adaptive, non-inferiority)
- Observational (cohort, case-control, cross-sectional, nested case-control)
- Survival (Kaplan-Meier, Cox PH, competing risks, time-varying, landmark, joint models)
- Diagnostic accuracy (ROC, calibration, DCA, NRI/IDI)
- Causal inference (PSM, IPTW, Entropy Balancing, doubly-robust, instrumental variables)
- Prediction models (TRIPOD — derivation, internal/external validation, bootstrap, calibration plot)

ตามมาตรฐาน **CONSORT 2010, STROBE 2007, STARD 2015, TRIPOD 2015, SAMPL**

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
| Reproducibility | seed-locked do-file + log file |
| Software statement | "STATA version 18 (StataCorp, College Station, TX, USA)" |

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

```stata
version 18
set more off
set scheme s2color

// ─── 1. Data overview ───────────────────────────────────────────
describe
codebook, compact
misstable summarize   // missing data pattern

// ─── 2. Normality (run ก่อนเลือก parametric vs non-parametric) ──
// Shapiro-Wilk (n < 2000)
swilk age bmi creatinine

// Skewness-Kurtosis test (n ≥ 2000)
sktest age bmi creatinine

// QQ plot
foreach v of varlist age bmi creatinine {
    qnorm `v', title("Normal Q-Q plot: `v'") scheme(s2color)
    graph export "qnorm_`v'.tif", as(tif) replace width(3000)
}

// ─── 3. Equal variance (ก่อน t-test / ANOVA) ────────────────────
// Levene / Brown-Forsythe
robvar outcome, by(treatment)

// ─── 4. VIF (หลัง regression — multicollinearity) ───────────────
regress outcome i.treatment age bmi i.sex
vif
// VIF > 5 = concern; VIF > 10 = serious problem

// ─── 5. Linearity of continuous predictors (logistic) ───────────
// Box-Tidwell: เพิ่ม interaction term predictor × ln(predictor)
gen ln_age = ln(age)
gen age_ln_age = age * ln_age
logit outcome age ln_age age_ln_age    // ถ้า age_ln_age p < 0.05 → non-linear
drop ln_age age_ln_age

// ─── 6. PH assumption (Cox) — Schoenfeld residuals ──────────────
// ดู Track D

// ─── 7. Calibration (prediction model) ──────────────────────────
// ดู Track E
```

---

## Sample Size & Power (เสนอก่อนทุก Track)

```stata
// ─── Continuous outcome (two independent groups) ─────────────────
power twomeans 0 0.5, sd(1) alpha(0.05) power(0.80)
// หรือ: power twomeans mu1 mu2, sd(sd) n(n_per_group)  // คำนวณ power

// ─── Binary outcome (two proportions) ────────────────────────────
power twoproportions 0.20 0.35, alpha(0.05) power(0.80)

// ─── Survival outcome (Schoenfeld formula via STATA) ─────────────
power cox 0.65, failprob(0.60) alpha(0.05) power(0.80)
// HR = 0.65; failprob = prob of event in control arm

// ─── Diagnostic accuracy ─────────────────────────────────────────
// AUC comparison (two correlated ROCs)
power roc 0.70 0.80, alpha(0.05) power(0.80)

// ─── One proportion ──────────────────────────────────────────────
power oneproportion 0.50 0.65, alpha(0.05) power(0.80) onesided

// ─── Paired t-test ───────────────────────────────────────────────
power pairedmeans 5 7, sddiff(3) alpha(0.05) power(0.80)
```

ทุก protocol ต้องมี sample-size justification — ระบุใน Methods section

---

## Track A — RCT Analysis (CONSORT 2010)

### A1. Table 1 (Baseline + SMD) — Publication-grade

```stata
// ─── ติดตั้ง packages ที่จำเป็น ──────────────────────────────────
ssc install table1_mc, replace     // Table 1 with SMD
ssc install stddiff, replace       // Standardized Mean Difference

// ─── ตรวจสอบ normality เพื่อเลือก parametric/non-parametric ─────
foreach v of varlist age bmi creatinine {
    swilk `v'
}

// ─── สร้าง Table 1 ───────────────────────────────────────────────
// ตัวแปร continuous (normal): mean ± SD
// ตัวแปร continuous (non-normal): median (IQR)
// ตัวแปร categorical: n (%)

table1_mc,                         ///
    by(treatment)                   ///
    vars(                           ///
        age        contn            ///   continuous normal
        bmi        contn            ///
        sbp        contln           ///   continuous non-normal (median IQR)
        sex        cat              ///   categorical
        diabetes   cat              ///
        smoking    cat              ///
    )                               ///
    smd                             ///   Q1: ใช้ SMD แทน p-value ใน RCT
    onecol                          ///
    saving("table1.xlsx", replace)

// ─── SMD ทุกตัวแปร ───────────────────────────────────────────────
// Q1: SMD ควร < 0.1 ทุกตัวแปร
stddiff age bmi sbp i.sex i.diabetes i.smoking, by(treatment) abs

// ─── Missing data report ──────────────────────────────────────────
misstable summarize age bmi sbp sex diabetes smoking
```

> **Q1 reminder**: ใน RCT ไม่ควรเน้น p-value สำหรับ baseline — ใช้ **SMD** (Pocock, 2002)

### A2. Primary outcome analysis (ITT)

```stata
// ─── ITT analysis (primary) ───────────────────────────────────────
// All randomised regardless of compliance
// Missing → handle with MICE (ดู Phase Final สำหรับ mi)

// Binary primary outcome: Risk Ratio / Odds Ratio
cs outcome treatment          // Risk difference + RR (cohort)
cc outcome treatment          // OR (case-control/RCT binary)

// Adjusted analysis (multivariable)
logistic outcome i.treatment age bmi i.sex i.diabetes, vce(robust)
estimates store model_adj
outreg2 using table2_primary.xlsx, eform replace excel

// Continuous primary outcome
regress outcome i.treatment age bmi i.sex, vce(robust)

// ─── Per-protocol (secondary) ─────────────────────────────────────
logistic outcome i.treatment age bmi i.sex i.diabetes  ///
    if pp_population == 1, vce(robust)

// Effect estimate format:
// Binary:     OR / RR + 95% CI + p (exact 3 decimal)
// Continuous: mean diff + 95% CI + p
// Survival:   HR + 95% CI + log-rank p
```

### A3. Subgroup analysis (forest plot, interaction p)

```stata
// ─── Pre-specified subgroups only (≤ 5-7) ────────────────────────
ssc install metan, replace   // forest plot

// Fit model per subgroup + test interaction
foreach subgrp in male female {
    logistic outcome i.treatment age bmi i.diabetes  ///
        if sex_label == "`subgrp'"
    estimates store est_`subgrp'
}

// Interaction test (effect modifier)
logistic outcome i.treatment##i.sex age bmi i.diabetes, vce(robust)
// p for interaction = p(1.treatment#1.sex)

// Forest plot สำหรับ subgroup (HR scale)
// เตรียมข้อมูลในรูป matrix แล้วใช้ metan
metan hr ll ul, lcols(subgroup n_subgroup) ///
    effect("Odds Ratio") null(1)            ///
    diamond scheme(s2color)                 ///
    title("Subgroup Analysis — Primary Endpoint")
graph export "fig_subgroup.tif", as(tif) replace width(3000)
```

### A4. Multiplicity control (mandatory in Q1)

| Strategy | When |
|---|---|
| Pre-specified single primary | preferred — no adjustment needed |
| Hierarchical testing | multiple endpoints with priority order |
| Bonferroni | conservative, all-or-none |
| Holm / Hochberg | step-down, more power |
| FDR (Benjamini-Hochberg) | exploratory / many tests |

```stata
// Bonferroni correction (manual)
local alpha_adj = 0.05 / 3    // 3 endpoints
di "Bonferroni-adjusted alpha = " `alpha_adj'

// Multiple testing via STATA holm package
ssc install holm, replace
holm p1 p2 p3, method(holm)
```

ระบุใน Methods + SAP

---

## Track B — Cohort Analysis (STROBE 2007)

### B1. Incidence rate

```stata
// Incidence Rate per 1000 person-years
stset futime, failure(event==1) id(id) scale(365.25)
stptime, by(treatment) per(1000) dd(2)
// Output: incidence rate + 95% CI (exact Poisson)
```

### B2. Risk Ratio / Hazard Ratio

```stata
// ─── Modified Poisson regression → RR (preferred for cohort) ────
// Zou 2004: ใช้ Poisson + robust SE แทน log-binomial
poisson outcome i.treatment age bmi i.sex i.diabetes, vce(robust)
// Report as RR: exp(coef)
eststo model_rr
esttab model_rr, eform ci(3) p(3) label

// ─── Unadjusted + adjusted (Q1 ต้องแสดงทั้งคู่) ──────────────────
// Unadjusted
poisson outcome i.treatment, vce(robust)
eststo model_crude

// Adjusted
poisson outcome i.treatment age bmi i.sex i.diabetes, vce(robust)
eststo model_adj

esttab model_crude model_adj using table_rr.xlsx,          ///
    eform ci(3) p(3) label replace excel
```

> ใช้ **modified Poisson** สำหรับ RR ใน cohort (Zou 2004) แทน log-binomial (convergence issues)

---

## Track C — Case-Control / Cross-sectional

```stata
// ─── 2×2 table: Univariable ───────────────────────────────────────
tabulate case exposure, chi2 exact or
// OR + 95% CI + p

// ─── Multivariable Logistic Regression ────────────────────────────
// Univariable ก่อน
foreach v of varlist age bmi i.sex i.diabetes i.smoking {
    quietly logistic case `v'
    estimates store uni_`v'
}

// Multivariable (pre-specified confounders)
logistic case age bmi i.sex i.diabetes i.smoking
estimates store multi
lrtest uni_age multi    // LR test for model comparison

// OR + 95% CI
lincom age, eform       // OR for continuous
lincom 1.sex, eform     // OR for categorical

// ─── Cross-sectional: Prevalence Ratio via modified Poisson ──────
poisson case i.exposure age bmi i.sex, vce(robust)
// Report as PR (not OR)
```

> **Q1 standard**: แสดง **univariable + multivariable** ทั้งคู่ในตาราง

---

## Track D — Survival Analysis

```stata
// ─── Set survival data ────────────────────────────────────────────
stset time, failure(event==1) id(id) origin(time enterdate) ///
    enter(time enterdate) exit(time exitdate)

// ─── Kaplan-Meier curve (Q1 mandatory: at-risk table + CI) ───────
sts graph, by(treatment)                    ///
    scheme(s2color)                          ///
    risktable(, order(1 "Control" 2 "Treatment")) ///
    ci                                       ///
    title("Overall Survival")               ///
    ytitle("Survival Probability")          ///
    xtitle("Time (months)")                 ///
    ylabel(0(.2)1, format(%3.1f))           ///
    xlabel(0(12)60)                         ///
    legend(order(1 "Control" 3 "Treatment") pos(1) ring(0))
graph export "fig1_km.tif", as(tif) replace width(3000)

// ─── Log-rank test ────────────────────────────────────────────────
sts test treatment                          // log-rank
sts test treatment, wilcoxon               // Peto-Peto (early differences)

// ─── Median survival + landmark rates ────────────────────────────
sts list, by(treatment) at(12 24 36 60)
// Output: S(t) + 95% CI at each landmark time

// ─── Cox PH Model ────────────────────────────────────────────────
stcox i.treatment age bmi i.sex i.diabetes, vce(robust) efron
estat concordance         // C-statistic (discrimination)

// Extract HR + 95% CI + p
stcox i.treatment age bmi i.sex i.diabetes, vce(robust)
lincom 1.treatment, eform      // HR for treatment

// ─── PH assumption (MANDATORY for Q1) ────────────────────────────
estat phtest, detail       // Schoenfeld residuals — all p > 0.05 = PH holds

// log-log plot (visual check)
stphplot, by(treatment) scheme(s2color)    ///
    title("Log-log Plot: PH Assumption Check")
graph export "fig_loglog.tif", as(tif) replace width(3000)

// ─── PH violation → stratified Cox ───────────────────────────────
stcox i.treatment age bmi, strata(sex) vce(robust)

// ─── Competing risks (Fine-Gray) ─────────────────────────────────
// STATA 11+ native command
stset time, failure(event) id(id)
// event: 1=event of interest, 2=competing event, 0=censored
stcrreg i.treatment age bmi i.sex, compete(event==2)
// Reports Subhazard Ratio (SHR) + 95% CI

// Cumulative incidence function
stcompet cif1=ci, compet1(2) by(treatment)

// ─── Landmark analysis ────────────────────────────────────────────
// เลือกเฉพาะผู้ป่วยที่รอดถึง t* = 12 months
stset time, failure(event==1) id(id)
keep if _t0 == 0
stsplit landmark_split, at(12)
keep if landmark_split == 12
replace _t = _t - 12
replace _t0 = 0
stcox i.treatment age bmi i.sex
```

ดู `references/survival-analysis.md` สำหรับ competing risks, joint models, time-varying covariates

---

## Track E — Diagnostic Accuracy (STARD 2015)

```stata
// ─── ROC curve + AUC (mandatory CI for Q1) ────────────────────────
roctab disease score, summary graph ///
    scheme(s2color)                   ///
    title("ROC Curve: Biomarker Score")
graph export "fig_roc.tif", as(tif) replace width(3000)

// AUC + 95% CI + Youden optimal cutpoint
roctab disease score, summary detail

// ─── Compare two AUCs (DeLong test) ──────────────────────────────
roccomp disease score1 score2, ///
    graph scheme(s2color)       ///
    title("ROC Comparison")
graph export "fig_roc_compare.tif", as(tif) replace width(3000)
// Reports z-statistic + p for AUC difference

// ─── Sensitivity / Specificity at chosen cutpoint ────────────────
diagt disease score_binary     // 2×2 table: Sn, Sp, PPV, NPV, LR+, LR-

// Wilson CI for Sn/Sp
cii 200 150, wilson             // 200 positives, 150 correctly identified

// ─── Calibration (MANDATORY for Q1 prediction models) ────────────
logistic disease score1 score2 age bmi
predict pred_prob, pr

// Hosmer-Lemeshow test
estat gof, group(10)           // HL χ²(8); p > 0.05 = good calibration

// Calibration plot
ssc install pmcalplot, replace
pmcalplot pred_prob disease,   ///
    ci title("Calibration Plot") ///
    scheme(s2color)
graph export "fig_calibration.tif", as(tif) replace width(3000)

// Brier score
gen brier_i = (disease - pred_prob)^2
summarize brier_i
// Brier score = mean(brier_i); < 0.25 = better than chance

// ─── Decision Curve Analysis (MANDATORY Q1 prediction) ───────────
ssc install dca, replace
dca disease pred_prob,          ///
    smooth                       ///
    xrange(0.05 0.35)            ///
    title("Decision Curve Analysis") scheme(s2color)
graph export "fig_dca.tif", as(tif) replace width(3000)

// ─── NRI / IDI (when comparing two models) ────────────────────────
ssc install nri_ipcw, replace   // or use manual calculation
```

> **Q1 diagnostic mandatory**: ROC + 95% CI (DeLong) + calibration + DCA + reclassification (NRI/IDI)

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

### F2. PSM (STATA 18 built-in)

```stata
// ─── Built-in teffects (STATA 13+) ───────────────────────────────
teffects psmatch (outcome) (treatment age bmi i.sex i.diabetes), ///
    atet caliper(0.25) nneighbor(1)
// ATET = Average Treatment Effect in the Treated

// Check balance after matching
tebalance summarize    // SMD ทุกตัวแปร
tebalance overid       // formal balance test

// ─── psmatch2 (user-written, more options) ────────────────────────
ssc install psmatch2, replace
psmatch2 treatment age bmi i.sex i.diabetes,  ///
    outcome(outcome)                            ///
    logit caliper(0.2) ate noreplace            ///
    neighbor(1)
pstest age bmi sex diabetes          // balance check + SMD

// ─── IPTW (STATA 18 built-in) ─────────────────────────────────────
teffects ipw (outcome) (treatment age bmi i.sex i.diabetes)
tebalance summarize

// ─── Manual IPTW with stabilized weights ─────────────────────────
logit treatment age bmi i.sex i.diabetes
predict ps, pr
gen iptw_s = treatment*(1/ps)*r(mean_treatment) + ///
             (1-treatment)*(1/(1-ps))*(1-r(mean_treatment))
// Stabilized weights: ลด influence of extreme weights
summ iptw_s, detail
// Trim weights at 1st-99th percentile
centile iptw_s, centile(1 99)
replace iptw_s = r(c_1)  if iptw_s < r(c_1)
replace iptw_s = r(c_99) if iptw_s > r(c_99)

// Weighted Cox regression (IPTW)
stset time, failure(event==1) id(id)
stcox i.treatment age bmi i.sex, vce(robust) [pweight=iptw_s]

// ─── Entropy Balancing ────────────────────────────────────────────
ssc install ebalance, replace
ebalance treatment age bmi i.sex i.diabetes, targets(1) generate(eb_weight)
// targets(1) = match first moments (means)
// targets(2) = match means + variances
```

### F3. Balance diagnostics (Love Plot)

```stata
// ─── SMD ก่อนและหลัง balancing ────────────────────────────────────
ssc install stddiff, replace

// SMD before
stddiff age bmi i.sex i.diabetes, by(treatment) abs
matrix smd_before = r(stddiff)

// SMD after (PSM matched sample)
stddiff age bmi i.sex i.diabetes if matched==1, by(treatment) abs
matrix smd_after = r(stddiff)

// ─── Love Plot (STATA 18) ─────────────────────────────────────────
// เตรียมข้อมูลสำหรับ Love Plot
preserve
    clear
    input str20 varname smd_b smd_a
    "Age"        0.45 0.03
    "BMI"        0.38 0.05
    "Male sex"   0.52 0.04
    "Diabetes"   0.41 0.06
    end
    
    gen obs_n = _n
    twoway (scatter obs_n smd_b, msymbol(Oh) mcolor(maroon) msize(medium)) ///
           (scatter obs_n smd_a, msymbol(O)  mcolor(navy)   msize(medium)) ///
           (pcspike obs_n smd_b obs_n smd_a, lcolor(gs10) lwidth(thin)),   ///
        xline(0 0.1, lcolor(black) lpattern(dash))                         ///
        xlabel(0(.1).6) ytitle("") xtitle("Standardized Mean Difference")  ///
        ylabel(1/4, valuelabel angle(0))                                    ///
        legend(order(1 "Before Balancing" 2 "After Balancing") ring(0) pos(4)) ///
        title("Love Plot: Covariate Balance") scheme(s2color)
    graph export "fig_love.tif", as(tif) replace width(3000)
restore

// Q1 standard: ALL SMD < 0.1 after balancing
```

### F4. E-value (Q1 expects for observational causal inference)

```stata
// E-value = RR + sqrt(RR × (RR - 1))
// ใช้หลัง Cox/logistic; RR ≈ HR ถ้า rare outcome
local hr = exp(_b[1.treatment])
local evalue = `hr' + sqrt(`hr' * (`hr' - 1))
di "E-value = " `evalue'
di "ต้องการ unmeasured confounder ที่ associaton ≥ " `evalue' " กับทั้ง exposure และ outcome"

// หรือใช้ package
ssc install evalue, replace
evalue or 1.8, ll(1.2)     // E-value สำหรับ OR 1.8 (95% CI LL = 1.2)
```

### F5. Reporting (paste-ready)
```
"We estimated the average treatment effect using inverse probability
of treatment weighting (IPTW) with stabilised weights derived from a
logistic regression of treatment on baseline covariates [list].
Standardised mean differences (SMD) were used to assess balance,
with SMD < 0.1 considered adequate (Austin, 2009). Weighted Cox
regression with robust variance was used for the outcome model.
E-value was calculated to quantify sensitivity to unmeasured
confounding (VanderWeele & Ding, 2017). All analyses were performed
using STATA version 18 (StataCorp, College Station, TX, USA)."
```

---

## Track G — Prediction Model (TRIPOD 2015)

```stata
// ─── Step 1: Derivation cohort ────────────────────────────────────
logistic outcome age bmi i.sex i.diabetes creatinine
predict pred_prob, pr
estimates store model_derivation

// ─── Step 2: Internal validation (Bootstrap optimism correction) ──
// B = 1000 bootstrap resamples
ssc install bsvalidation, replace   // or manual bootstrap
bootstrap c=e(C), reps(1000) seed(42): logistic outcome age bmi i.sex i.diabetes creatinine
// Optimism-corrected C-statistic = apparent C − mean(optimism)

// Manual bootstrap approach
quietly {
    logistic outcome age bmi i.sex i.diabetes creatinine
    estat concordance
    scalar c_apparent = r(C)
}

// Bootstrap
set seed 42
bootstrap c_boot=r(C), reps(1000): {
    preserve
    bsample
    quietly logistic outcome age bmi i.sex i.diabetes creatinine
    estat concordance
    restore
}
scalar optimism = e(b)[1,1] - c_apparent
scalar c_corrected = c_apparent - optimism
di "Optimism-corrected C = " c_corrected

// ─── Step 3: External validation ──────────────────────────────────
// Apply derived model to external cohort
// (model stored as equation from derivation)
logistic outcome age bmi i.sex i.diabetes creatinine using derivation_model.ster
predict pred_prob_ext, pr

// Discrimination in external cohort
estat concordance

// Calibration in external cohort
pmcalplot pred_prob_ext outcome, ci scheme(s2color)
graph export "fig_calibration_ext.tif", as(tif) replace width(3000)
estat gof, group(10)

// ─── Step 4: DCA ──────────────────────────────────────────────────
dca outcome pred_prob pred_prob_ext, smooth scheme(s2color)
graph export "fig_dca_comparison.tif", as(tif) replace width(3000)
```

---

## Track H — Meta-analysis / Systematic Review (PRISMA 2020)

```stata
// ─── Random-effects meta-analysis ────────────────────────────────
ssc install metan, replace
ssc install metafunnel, replace

// Input: study, effect, lower CI, upper CI
// OR format
metan or ll ul, random                     ///
    label(namevar=study)                    ///
    effect("Odds Ratio")                    ///
    null(1) xlog                            ///
    forestplot(scheme(s2color))             ///
    title("Meta-analysis: Odds Ratio")
graph export "fig_forest_meta.tif", as(tif) replace width(3000)

// Heterogeneity: I², τ², Q-test → output ใน metan

// ─── Publication bias ─────────────────────────────────────────────
metafunnel or se_log_or, scheme(s2color)   // Funnel plot
graph export "fig_funnel.tif", as(tif) replace width(3000)

// Egger's test
metabias or se_log_or, egger               // Egger's regression test
// หรือ Begg's test
metabias or se_log_or, begg

// Trim-and-fill
metatrim or se_log_or, reffect funnel
```

PRISMA flow diagram numbers + risk-of-bias (RoB 2 / ROBINS-I) table mandatory

---

## Track I — Non-inferiority / Equivalence RCT

```stata
// ─── Pre-specify ──────────────────────────────────────────────────
// Δ (non-inferiority margin) ต้องระบุใน SAP พร้อม clinical justification

local delta = -0.05    // NI margin (e.g., -5% for absolute risk difference)

// ─── Test non-inferiority ─────────────────────────────────────────
// สำหรับ binary: แสดง 95% CI ของ risk difference
// ถ้า lower bound > Δ → non-inferiority established

// Risk difference + 95% CI
cs outcome treatment, woolf   // Woolf CI
// หรือ
prtest outcome if treatment==1 : outcome if treatment==0

// ─── One-sided 97.5% CI (= two-sided 95% lower bound) ─────────────
regress outcome i.treatment, vce(robust)
lincom 1.treatment           // รับ 95% CI; lower bound ต้อง > Δ

// ─── NI + superiority hierarchy (CONSORT-NI 2012) ─────────────────
// Report BOTH ITT and PP analyses
// ถ้า NI → test superiority (hierarchical)
```

---

## Track J — Bayesian Analysis

```stata
// STATA 16+ built-in Bayesian analysis
bayes, rseed(42) mcmcsize(10000) burnin(5000):          ///
    logit outcome i.treatment age bmi i.sex i.diabetes

// Output: posterior mean, 95% credible interval, P(β > 0)
bayesstats summary
bayesgraph diagnostics   // convergence: trace, autocorrelation, density

// Prior sensitivity
bayes, prior({outcome: i.treatment}, normal(0, 10)) rseed(42): ///
    logit outcome i.treatment age bmi i.sex i.diabetes
// Compare with diffuse prior

// MCMC convergence diagnostics
bayesstats ess         // ESS > 1000 per parameter
bayesstats rhat        // R-hat < 1.01 = converged
```

---

## Figure Standards — Actual STATA 18

ดูรายละเอียดใน `../manuscript-writer/references/stata-figure-style.md`

| Figure | STATA 18 Command | Notes |
|---|---|---|
| KM curve | `sts graph, risktable ci` | at-risk table + CI band + scheme(s2color) |
| ROC | `roctab` / `roccomp` | AUC + 95% CI + DeLong test |
| Forest | `metan` (SSC) | diamond + I² + weights |
| Calibration | `pmcalplot` (SSC) | observed vs predicted + density |
| DCA | `dca` (SSC) | net benefit vs threshold |
| Love plot | `twoway scatter + pcspike` | SMD before/after |
| Funnel | `metafunnel` (SSC) | for meta-analysis |
| Bland-Altman | `batplot` (SSC) | mean ± 1.96 SD limits |

```stata
// ─── Standard export settings (ALL figures) ──────────────────────
// ≥300 DPI: width(3000) = 10 inches @ 300 DPI
graph export "fig_name.tif", as(tif) replace width(3000)

// ─── Always set before figures ────────────────────────────────────
set scheme s2color
```

> **Q1 mandatory**: ≥300 DPI, TIFF, s2color scheme, label QC, color-blind safe palette (s2color ผ่านแล้ว)

---

## Statistical Reporting — Q1 Format

```
Continuous (normal):     mean ± SD
Continuous (non-normal): median (IQR) หรือ median (Q1–Q3)
Categorical:             n (%)
Effect (binary):         OR / RR / RD (95% CI)
Effect (continuous):     mean difference (95% CI)
Survival:                HR (95% CI); median (95% CI) time
Diagnostic:              AUC 0.XX (95% CI X.XX–X.XX); Sn XX%, Sp XX%
P-value:                 exact 3 decimals; <0.001 ถ้าน้อยกว่า
                         NEVER "NS"; NEVER "p = 0.000"
Sample size:             ใส่ n= ทุก column header ในตาราง
Missing:                 รายงาน n analysed เมื่อต่างจาก n randomised
Software:                "STATA version 18 (StataCorp, College Station, TX, USA)"
```

---

## Multiple Imputation (MICE) — สำหรับ Missing Data

```stata
// ─── ตรวจ missing ก่อน ────────────────────────────────────────────
misstable summarize
misstable patterns

// ─── MICE (Multiple Imputation by Chained Equations) ─────────────
mi set mlong
mi register imputed age bmi creatinine i.sex i.diabetes   // ตัวแปรที่มี missing
mi register regular outcome treatment time event           // ตัวแปรสมบูรณ์

// Impute m = 20 datasets (Q1 standard)
mi impute chained                                          ///
    (regress)  age bmi creatinine                          ///
    (logit)    sex diabetes                                ///
    = outcome treatment i.center,                          ///
    add(20) rseed(12345) augment

// ─── วิเคราะห์บน imputed datasets ────────────────────────────────
mi estimate, dots: logistic outcome i.treatment age bmi i.sex
mi estimate, dots: stcox i.treatment age bmi i.sex

// ─── Rubin's rules applied automatically ──────────────────────────
// STATA จัดการ pooling ให้อัตโนมัติ
```

---

## Sensitivity Analyses (mandatory for Q1)

Pre-specify ≥3 ของสิ่งต่อไปนี้:
1. ITT vs PP vs mITT (RCT)
2. Complete-case vs MICE imputed
3. With vs without outliers
4. Alternative model specification
5. Different cutpoint for continuous predictors
6. Subgroup analysis (limited, pre-specified)
7. E-value for unmeasured confounding (observational)
8. Different missing-data assumption (MAR vs MNAR delta)

```stata
// ─── Sensitivity: Complete case vs Imputed ────────────────────────
// Complete case
logistic outcome i.treatment age bmi i.sex if !missing(age, bmi)
eststo sens_cc

// Imputed
mi estimate: logistic outcome i.treatment age bmi i.sex
eststo sens_mi

// ─── Sensitivity: With vs without outliers ────────────────────────
// ระบุ outliers ด้วย Cook's distance หลัง regression
regress outcome i.treatment age bmi i.sex
predict cookd, cooksd
gen outlier = cookd > 4/e(N)
logistic outcome i.treatment age bmi i.sex if !outlier
eststo sens_no_outlier

// ─── เปรียบเทียบ sensitivity analyses ────────────────────────────
esttab sens_cc sens_mi sens_no_outlier, eform ci(3) p(3)
```

---

## Phase Final — Results Package Export

```stata
// ─── 1. Tables (putexcel) ─────────────────────────────────────────
putexcel set "tables_all.xlsx", replace
// table1, table2_outcomes, table3_multivariable → แต่ละ sheet

// ─── 2. Figures (TIFF ≥300 DPI) ──────────────────────────────────
// fig1_km.tif, fig2_roc.tif, fig3_forest.tif, ...
// Export ทุก figure ด้วย: graph export "name.tif", as(tif) replace width(3000)

// ─── 3. Results paragraph (paste-ready) ──────────────────────────
// บันทึก template ใน results_paragraph.txt

// ─── 4. Methods paragraph ─────────────────────────────────────────
// "...Statistical analyses were performed using STATA version 18
// (StataCorp, College Station, TX, USA)."

// ─── 5. SAP summary ───────────────────────────────────────────────
// sap_summary.md

// ─── 6. Sensitivity report ────────────────────────────────────────
// sensitivity_report.xlsx

// ─── 7. Do-file + Log ─────────────────────────────────────────────
log close
// Deliver: analysis_main.do + analysis.log
```

---

## หลักการสำคัญ (Q1 High-IF)

- **อย่า** ทำ post-hoc analysis โดยไม่ระบุว่าเป็น exploratory
- **อย่า** ใช้ p-value เป็นเกณฑ์เดียวในการตัดสิน clinical significance
- **อย่า** dichotomize continuous variables โดยไม่มีเหตุผล
- **อย่า** report only adjusted effect — แสดง unadjusted ด้วยเสมอ
- **ต้อง** ระบุ multiplicity adjustment เมื่อมี ≥2 endpoints
- **ต้อง** ทำ assumption checking ก่อนใช้ parametric test
- **ต้อง** ทำ sensitivity analyses (≥3)
- **ต้อง** ระบุ effect size + 95% CI ทุกผล (ไม่ใช่แค่ p)
- **ต้อง** ใช้ `set scheme s2color` + export ≥300 DPI TIFF ทุก figure
- **ต้อง** ระบุ "STATA version 18 (StataCorp, College Station, TX, USA)" ใน Methods

---

## Reference Files

| File | เนื้อหา |
|---|---|
| `references/inferential-stats.md` | t-test, ANOVA, regression, non-parametric — STATA 18 code |
| `references/survival-analysis.md` | KM, Cox, competing risks, landmark, joint models |
| `references/diagnostic-accuracy.md` | ROC, DeLong, calibration, DCA, NRI/IDI |
| `references/group-balancing.md` | PSM, IPTW, Entropy, CEM, ANCOVA, doubly-robust, E-value |
| `../manuscript-writer/references/stata-figure-style.md` | STATA 18 graph commands + export settings |
