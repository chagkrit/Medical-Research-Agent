# Group Balancing Methods — Medical Research Reference (STATA 18)
# วิธีปรับสมดุลกลุ่มสำหรับงานวิจัยเชิงสังเกต

> **ทุก code ในไฟล์นี้ใช้ STATA 18** — เริ่มทุก do-file ด้วย `version 18` + `set scheme s2color`
>
> **เมื่อไหร่ต้องใช้**: เมื่อเปรียบเทียบสองกลุ่มใน observational study แล้วพบว่า baseline characteristics แตกต่างกัน (SMD > 0.1 หรือ p < 0.05 ใน Table 1)

---

## 0. การเลือกวิธีที่เหมาะสม

| วิธี | เหมาะกับ | ข้อดี | ข้อเสีย |
|---|---|---|---|
| **PSM** (1:1 NN with caliper) | n ปานกลาง (200–2000), confounders ≤ 10 | ตีความง่าย, มาตรฐานในงานวิจัย | ทิ้ง unmatched → ลด sample size |
| **IPTW** (Inverse Probability of Treatment Weighting) | n ใหญ่, ต้องการ preserve ทุก observation | ไม่ทิ้งข้อมูล, เหมาะ survival analysis | อาจมี extreme weights → ต้อง trim |
| **Entropy Balancing** | confounders > 10, ต้องการ balance แม่นยำ | balance ดีที่สุด, ไม่ต้อง trim weights | ต้องการ package เพิ่มเติม |
| **CEM** (Coarsened Exact Matching) | ข้อมูลเป็น categorical หลัก | exact match, ชัดเจน | ข้อมูลสูญหายมากถ้า coarsening แรง |
| **ANCOVA** | RCT ที่มี baseline imbalance เล็กน้อย | ง่าย, ไม่ต้อง match | ไม่เหมาะ observational study ที่ confounding รุนแรง |

---

## 1. Propensity Score Matching (PSM) — แนะนำอันดับ 1

### 1.1 Built-in teffects (STATA 13+) — วิธีหลัก

```stata
version 18
set more off
set scheme s2color

// ─── PSM ด้วย teffects (built-in — แนะนำ) ──────────────────────
// ATET = Average Treatment Effect in the Treated (มาตรฐาน Q1)
teffects psmatch (outcome) (treatment age bmi i.sex i.diabetes i.smoking), ///
    atet                                 ///
    caliper(0.2)                         ///   caliper = 0.2 SD ของ logit(PS)
    nneighbor(1)                         ///   1:1 matching
    vce(robust)

// ─── Balance check หลัง matching ─────────────────────────────────
tebalance summarize                      // SMD ก่อนและหลัง matching
tebalance overid                         // formal balance test

// ─── Overlap (common support) ─────────────────────────────────────
teffects overlap, scheme(s2color) title("Propensity Score Distribution")
graph export "fig_ps_overlap.tif", as(tif) replace width(3000)
```

### 1.2 psmatch2 (user-written — ตัวเลือกเพิ่มเติม)

```stata
// ─── ติดตั้ง ──────────────────────────────────────────────────────
ssc install psmatch2, replace

// ─── ประมาณ PS ด้วย logistic regression ──────────────────────────
logit treatment age bmi i.sex i.diabetes i.smoking
predict ps, pr

// ตรวจ common support (overlap)
twoway (histogram ps if treatment==1, width(0.05) color(navy%60) ///
            legend(label(1 "Treatment")))                          ///
       (histogram ps if treatment==0, width(0.05) color(maroon%60) ///
            legend(label(2 "Control"))),                           ///
    scheme(s2color)                                                 ///
    title("Propensity Score Distribution: Common Support Check")    ///
    xtitle("Propensity Score") ytitle("Frequency")
graph export "fig_ps_hist.tif", as(tif) replace width(3000)

// ─── 1:1 Nearest-Neighbor Matching ────────────────────────────────
// caliper = 0.2 × SD(logit(PS)) — Austin 2011
psmatch2 treatment age bmi i.sex i.diabetes i.smoking, ///
    outcome(outcome)                                     ///
    logit                                                ///
    caliper(0.2)                                         ///
    neighbor(1)                                          ///
    noreplace                                            ///
    ate

// ─── Balance check ───────────────────────────────────────────────
pstest age bmi sex diabetes smoking   // SMD ทุกตัวแปร
// Q1 standard: ALL SMD < 0.1 after matching

// ─── Matched dataset ──────────────────────────────────────────────
keep if _weight != .   // เก็บเฉพาะ matched observations
// หรือ: psmatch2 สร้าง _weight variable สำหรับ weighted analysis
```

### 1.3 Balance Check + Love Plot

```stata
// ─── SMD ก่อนและหลัง matching ────────────────────────────────────
ssc install stddiff, replace
stddiff age bmi i.sex i.diabetes i.smoking, by(treatment) abs
matrix smd_before = r(stddiff)

// หลัง matching (ใช้ _weight ถ้าใช้ psmatch2)
stddiff age bmi i.sex i.diabetes i.smoking [pweight=_weight], by(treatment) abs
matrix smd_after = r(stddiff)

// ─── Love Plot ────────────────────────────────────────────────────
preserve
    clear
    input str25 varname smd_b smd_a
    "Age"              0.45  0.03
    "BMI"              0.38  0.05
    "Male sex"         0.52  0.04
    "Diabetes"         0.41  0.06
    "Smoking"          0.33  0.08
    "Hypertension"     0.28  0.07
    end
    
    gen y = _n
    label define varlbl 1 "Age" 2 "BMI" 3 "Male sex" ///
                        4 "Diabetes" 5 "Smoking" 6 "Hypertension"
    label values y varlbl
    
    twoway (scatter y smd_b, msymbol(Oh) mcolor(maroon) msize(medlarge))  ///
           (scatter y smd_a, msymbol(O)  mcolor(navy)   msize(medlarge))  ///
           (pcspike y smd_b y smd_a, lcolor(gs10) lwidth(thin)),          ///
        xline(0,   lcolor(black) lwidth(thin))                             ///
        xline(0.1, lcolor(black) lwidth(thin) lpattern(dash))             ///
        xlabel(0(.1).6, grid)                                              ///
        ylabel(1/6, valuelabel angle(0))                                   ///
        ytitle("") xtitle("Standardized Mean Difference (SMD)")            ///
        legend(order(1 "Before Matching" 2 "After Matching") ring(0) pos(4)) ///
        title("Love Plot: Covariate Balance") scheme(s2color)
    graph export "fig_love.tif", as(tif) replace width(3000)
restore
// Q1 standard: ALL SMD < 0.1 after balancing
```

---

## 2. IPTW — Inverse Probability of Treatment Weighting

> **เหมาะกับ**: n ใหญ่, ต้องการ preserve ทุก observation, survival analysis

```stata
// ─── Built-in teffects ipw ────────────────────────────────────────
teffects ipw (outcome) (treatment age bmi i.sex i.diabetes), ///
    atet vce(robust)
tebalance summarize

// ─── Manual IPTW with stabilized weights (แนะนำ) ─────────────────
logit treatment age bmi i.sex i.diabetes i.smoking
predict ps, pr

// Marginal probability of treatment (สำหรับ stabilized weights)
summarize treatment
scalar p_treat = r(mean)

// Stabilized IPTW
gen iptw_s = treatment * p_treat / ps + ///
             (1-treatment) * (1-p_treat) / (1-ps)

// ตรวจ extreme weights
summ iptw_s, detail
di "Extreme weights (>10): " (iptw_s > 10 & !missing(iptw_s))

// Trim weights ที่ percentile 1 และ 99 (ลด influence of extreme values)
centile iptw_s, centile(1 99)
replace iptw_s = r(c_1)  if iptw_s < r(c_1)
replace iptw_s = r(c_99) if iptw_s > r(c_99)

// ─── Weighted Analysis ────────────────────────────────────────────
// Weighted logistic regression
logistic outcome i.treatment age bmi i.sex [pweight=iptw_s], vce(robust)

// Weighted survival (Cox)
stset time, failure(event==1) id(id)
stcox i.treatment age bmi i.sex [pweight=iptw_s], vce(robust)

// ─── Balance check (weighted) ─────────────────────────────────────
stddiff age bmi i.sex i.diabetes [pweight=iptw_s], by(treatment) abs
// ทุก SMD ต้อง < 0.1
```

---

## 3. Entropy Balancing

> **เหมาะกับ**: confounders เยอะ, ต้องการ balance แม่นยำที่สุด (Hainmueller 2012)

```stata
// ─── ติดตั้ง ──────────────────────────────────────────────────────
ssc install ebalance, replace

// ─── Entropy Balancing ────────────────────────────────────────────
// targets(1) = match first moments (means only)
// targets(2) = match means + variances (more strict)
ebalance treatment age bmi i.sex i.diabetes i.smoking, ///
    targets(1) generate(eb_weight)

// ─── ตรวจ balance หลัง entropy balancing ─────────────────────────
// Q1 expects: ALL SMD < 0.05 (entropy balancing ทำได้ดีมาก)
stddiff age bmi i.sex i.diabetes i.smoking [pweight=eb_weight], ///
    by(treatment) abs

// ─── Weighted Analysis ────────────────────────────────────────────
logistic outcome i.treatment age bmi i.sex [pweight=eb_weight], vce(robust)
stcox i.treatment age bmi i.sex [pweight=eb_weight], vce(robust)
```

---

## 4. Coarsened Exact Matching (CEM)

> **เหมาะกับ**: ต้องการ exact match ในตัวแปรสำคัญ

```stata
// ─── ติดตั้ง ──────────────────────────────────────────────────────
ssc install cem, replace

// ─── CEM ──────────────────────────────────────────────────────────
// Exact match: sex, diabetes (categorical)
// Coarsened match: age → 5 bins, bmi → 4 bins
cem age (10 40 50 60 70 100) bmi (0 25 30 35 100) i.sex i.diabetes, ///
    treatment(treatment)

// Generate CEM weights
gen cem_w = cem_weight

// ─── ตรวจ balance ────────────────────────────────────────────────
stddiff age bmi i.sex i.diabetes [pweight=cem_w], by(treatment) abs

// ─── Weighted Analysis ────────────────────────────────────────────
logistic outcome i.treatment age bmi [pweight=cem_w], vce(robust)
```

---

## 5. ANCOVA — สำหรับ RCT ที่มี Baseline Imbalance

> **ใช้เมื่อ**: RCT ที่มี baseline characteristics แตกต่างเล็กน้อย (ไม่ใช่ observational study)

```stata
// ─── ANCOVA: outcome ~ treatment + baseline_covariates ────────────
regress outcome i.treatment age bmi i.sex i.diabetes, vce(robust)
// Treatment effect = coef(1.treatment) + 95% CI

lincom 1.treatment         // Adjusted treatment effect (mean difference)
// Adjusted MD = [coef], 95% CI [ll, ul], p = [p]

// ─── Binary outcome: Logistic ANCOVA ─────────────────────────────
logistic outcome i.treatment age bmi i.sex i.diabetes, vce(robust)
lincom 1.treatment, eform  // Adjusted OR

// ─── Continuous outcome (RCT primary analysis — CONSORT 2010) ─────
// ANCOVA ≥ t-test สำหรับ RCT เสมอ (VanBreukelen 2006)
regress endline i.treatment baseline, vce(robust)
// ควบคุม baseline value เสมอ
```

---

## 6. Doubly-Robust Estimation

```stata
// ─── teffects ra (regression adjustment) ──────────────────────────
teffects ra (outcome age bmi i.sex i.diabetes) (treatment), ///
    atet vce(robust)

// ─── teffects aipw (doubly-robust AIPW) ──────────────────────────
// ถูกต้องถ้า outcome model หรือ PS model ถูกต้องอย่างน้อย 1 อัน
teffects aipw (outcome age bmi i.sex i.diabetes) ///
              (treatment age bmi i.sex i.diabetes), ///
    atet vce(robust)
```

---

## 7. Sensitivity Analysis หลัง Balancing

### E-value (VanderWeele & Ding 2017) — Q1 mandatory สำหรับ observational

```stata
// ─── E-value ─────────────────────────────────────────────────────
ssc install evalue, replace

// หลัง Cox regression: HR + lower 95% CI
stcox i.treatment age bmi i.sex i.diabetes [pweight=iptw_s], vce(robust)
// สมมติ HR = 1.8, lower CI = 1.2
evalue hr 1.8, ll(1.2)
// Output: E-value for point estimate + for CI lower bound

// สำหรับ OR
evalue or 2.1, ll(1.5)

// Interpretation:
// E-value = minimum association (RR) an unmeasured confounder must have
// with both exposure AND outcome to explain away the result
di "An unmeasured confounder must have RR ≥ [E-value] with both treatment"
di "and outcome to fully explain the observed association"
```

### Rosenbaum Bounds (Hidden Bias Sensitivity)

```stata
// psmatch2 ต้องทำก่อน
ssc install rbounds, replace
rbounds outcome [fweight=_n], gamma(1(0.25)3)
// Γ = 1 = no hidden bias
// ถ้า p > 0.05 ที่ Γ ต่ำ = ผลไม่ robust ต่อ hidden bias
```

### Triangulation (แนะนำ Q1)

```stata
// วิเคราะห์ด้วยหลายวิธี → ถ้าผลตรงกัน = robust
// Method 1: PSM
// Method 2: IPTW
// Method 3: Entropy Balancing
// Method 4: ANCOVA adjustment

// สรุป: ถ้า 3/4 methods ให้ผลคล้ายกัน → ผลลัพธ์น่าเชื่อถือ
esttab psm_result iptw_result eb_result ancova_result, ///
    eform ci(3) p(3) label
```

---

## 8. Manuscript Reporting Template

### Methods Section

```
We used [Propensity Score Matching / IPTW / Entropy Balancing] to account for
baseline differences between groups. Propensity scores were estimated using
logistic regression, including [list of confounders] as covariates.

[For PSM]: A 1:1 nearest-neighbor matching algorithm with a caliper of 0.2 SD
of the logit-transformed propensity score was applied (Austin 2011), using the
teffects psmatch command in STATA version 18.

[For IPTW]: Stabilized inverse probability of treatment weights were calculated
and trimmed at the 1st and 99th percentiles to reduce the influence of extreme
weights. Weighted regression with robust variance (vce(robust)) was performed.

Covariate balance was assessed using standardised mean differences (SMD), with
SMD < 0.1 considered indicative of adequate balance. Sensitivity to unmeasured
confounding was assessed using the E-value (VanderWeele & Ding, 2017).
All analyses were performed using STATA version 18 (StataCorp, College Station,
TX, USA).
```

### Results Section

```
Before [matching/weighting], significant differences were observed between
the two groups in [list variables] (all SMD > 0.1, Table 1).
After [matching/weighting], baseline characteristics were well-balanced
(all SMD < 0.1, Table 2; Figure X, Love Plot).

[For PSM]: Of [N] treated patients, [n] (XX%) were successfully matched
to a control patient within the specified caliper of 0.2 SD.

The E-value for the main analysis was [X.X] (95% CI lower bound: [X.X]),
indicating that an unmeasured confounder associated with both treatment
and outcome by a risk ratio of [X.X]-fold would be required to explain away
the observed association.
```

### Table Template (Publication-ready)

```
Table 2. Baseline Characteristics Before and After Propensity Score Matching

Variable         | Before Matching          |        | After Matching           |
                 | Group A    | Group B    | SMD    | Group A    | Group B    | SMD
-----------------+------------+------------+--------+------------+------------+------
Age, years       | 65.2 ± 8.3 | 58.7 ± 9.1 | 0.75 ❌| 64.8 ± 8.5 | 64.5 ± 8.9 | 0.03 ✅
Male sex, n (%)  | 145 (72.5) | 89 (44.5)  | 0.59 ❌| 98 (65.3)  | 95 (63.3)  | 0.04 ✅
BMI, kg/m²       | 26.1 ± 4.2 | 28.3 ± 5.1 | 0.47 ❌| 26.5 ± 4.3 | 26.8 ± 4.6 | 0.07 ✅
Diabetes, n (%)  | 82 (41.0)  | 45 (22.5)  | 0.41 ❌| 56 (37.3)  | 53 (35.3)  | 0.04 ✅

SMD: Standardized Mean Difference (< 0.1 considered balanced ✅)
Statistical analysis: STATA version 18 (StataCorp, College Station, TX, USA)
```

---

## References

1. Austin PC. An introduction to propensity score methods for reducing the effects of confounding in observational studies. *Multivariate Behav Res.* 2011;46(3):399-424.
2. Hainmueller J. Entropy balancing for causal effects. *Polit Anal.* 2012;20(1):25-46.
3. Iacus SM, King G, Porro G. Causal inference without balance checking: Coarsened exact matching. *Polit Anal.* 2012;20(1):1-24.
4. VanderWeele TJ, Ding P. Sensitivity analysis in observational research: introducing the E-value. *Ann Intern Med.* 2017;167(4):268-274.
5. Stuart EA. Matching methods for causal inference: A review and a look forward. *Stat Sci.* 2010;25(1):1-21.
