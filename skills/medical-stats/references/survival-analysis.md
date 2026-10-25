# Survival Analysis — Medical Research Reference (STATA 18)
# การวิเคราะห์ Survival สำหรับงานวิจัยทางการแพทย์

> **ทุก code ในไฟล์นี้ใช้ STATA 18** — เริ่มทุก do-file ด้วย `version 18` + `set scheme s2color`

## 1. Kaplan-Meier Analysis

```stata
version 18
set more off
set scheme s2color

// ─── Set survival data ────────────────────────────────────────────
// time: follow-up time (days/months)
// event: 1 = event occurred, 0 = censored
stset time, failure(event==1) id(id) scale(30.4375)   // scale=30.44 → months

// ─── KM overall (single curve) ────────────────────────────────────
sts list                   // survival table
sts list, at(12 24 36 60)  // landmark rates at 1, 2, 3, 5 years

// Median survival + 95% CI
stci, medianonly

// ─── KM by group — Publication-quality ────────────────────────────
sts graph, by(treatment)                        ///
    scheme(s2color)                              ///
    ci                                           ///
    risktable(, order(1 "Control" 2 "Treatment")  ///
               title("No. at risk"))             ///
    plot1opts(lwidth(medthick) lcolor(navy))     ///
    plot2opts(lwidth(medthick) lcolor(maroon))   ///
    title("Overall Survival by Treatment Group") ///
    ytitle("Survival Probability")               ///
    xtitle("Time (months)")                      ///
    ylabel(0(0.2)1, format(%3.1f) grid)          ///
    xlabel(0(12)60, grid)                        ///
    legend(order(1 "Control" 3 "Treatment") ring(0) pos(1))
graph export "fig1_km.tif", as(tif) replace width(3000)

// ─── Landmark survival rates ──────────────────────────────────────
sts list, by(treatment) at(12 24 36 60)
// Output: S(t) + 95% CI per group at each time point
```

## 2. Log-rank Test Variations

```stata
// ─── Standard log-rank ───────────────────────────────────────────
sts test treatment
// Output: chi2, df, p-value

// ─── Weighted log-rank ────────────────────────────────────────────
// Gehan-Wilcoxon (emphasizes early differences)
sts test treatment, wilcoxon

// Tarone-Ware (compromise)
sts test treatment, tware

// ─── More than 2 groups ───────────────────────────────────────────
sts test treatgroup                           // overall test
// ถ้า significant → pairwise with Bonferroni
forvalues i = 1/3 {
    forvalues j = 2/3 {
        if `i' < `j' {
            sts test treatment if inlist(treatment, `i', `j')
        }
    }
}

// ─── Stratified log-rank ──────────────────────────────────────────
sts test treatment, strata(center)
```

## 3. Cox Proportional Hazards Model

```stata
// ─── Unadjusted Cox ───────────────────────────────────────────────
stcox i.treatment
// Output: HR (directly as Haz. Ratio) + 95% CI + p

// ─── Adjusted Cox ────────────────────────────────────────────────
stcox i.treatment age bmi i.sex i.diabetes, vce(robust)
// vce(robust): sandwich SE for robustness

// Extract HR + 95% CI
lincom 1.treatment, eform        // HR for treatment (vs reference)

// Unadjusted + Adjusted (show both for Q1)
stcox i.treatment
eststo cox_crude
stcox i.treatment age bmi i.sex i.diabetes, vce(robust)
eststo cox_adj

esttab cox_crude cox_adj using "table_cox.xlsx",        ///
    eform ci(3) p(3) label replace excel                 ///
    title("Cox Proportional Hazards Regression")

// ─── Baseline hazard ──────────────────────────────────────────────
stcox i.treatment age bmi i.sex i.diabetes, basehc(H0)  // cumulative baseline hazard

// ─── C-statistic (Harrell's C) ────────────────────────────────────
stcox i.treatment age bmi i.sex i.diabetes
estat concordance
```

### Checking Proportional Hazards Assumption (MANDATORY for Q1)

```stata
// ─── Schoenfeld residuals (formal test) ───────────────────────────
stcox i.treatment age bmi i.sex i.diabetes, vce(robust)
estat phtest, detail
// H0: PH holds; p > 0.05 per covariate AND global

// ─── Log-log plot (visual, complementary) ────────────────────────
stphplot, by(treatment) scheme(s2color)           ///
    title("Log-log Plot: Proportional Hazards Check")
graph export "fig_loglog.tif", as(tif) replace width(3000)
// เส้นขนานกัน = PH satisfied

// ─── Scaled Schoenfeld residuals plot ────────────────────────────
quietly stcox i.treatment age bmi i.sex i.diabetes
estat phtest, plot(1.treatment) scheme(s2color)
graph export "fig_schoenfeld.tif", as(tif) replace width(3000)
```

**ถ้า PH violated — วิธีแก้:**

```stata
// Option 1: Stratified Cox (stratify by violating variable)
stcox i.treatment age bmi, strata(sex) vce(robust)

// Option 2: Time-varying coefficient
stsplit split_t, at(12)    // split at 12 months
gen treat_early = (treatment==1) & (split_t < 12)
gen treat_late  = (treatment==1) & (split_t >= 12)
stcox treat_early treat_late age bmi i.sex, vce(robust)

// Option 3: Royston-Parmar flexible parametric model
ssc install stpm2, replace
stpm2 i.treatment age bmi i.sex, df(4) scale(hazard) eform
```

### Stratified Cox Model

```stata
// สำหรับ multicenter หรือ stratification by nuisance variable
stcox i.treatment age bmi, strata(center) vce(robust)
// center-specific baseline hazard; pooled treatment effect
```

## 4. Competing Risks Analysis

```stata
// เมื่อมี competing events (เช่น death from other cause)
// event coding: 1 = event of interest, 2 = competing event, 0 = censored

stset time, failure(event) id(id)

// ─── Cumulative Incidence Function (CIF) ──────────────────────────
stcompet cif_interest=ci, compet1(2)    // CIF for event of interest
stcompet cif_compete=ci,  compet1(1)    // CIF for competing event

// Plot CIF by group
sts graph, failure by(treatment)         ///
    competing(2)                          ///
    scheme(s2color)                       ///
    title("Cumulative Incidence Function")
graph export "fig_cif.tif", as(tif) replace width(3000)

// ─── Fine-Gray Subdistribution Hazard Model ───────────────────────
// STATA 11+ native command
stcrreg i.treatment age bmi i.sex, compete(event==2)
// Output: Subhazard Ratio (SHR) + 95% CI + p

// Q1 reporting: รายงานทั้ง cause-specific HR (Cox) และ SHR (Fine-Gray)
```

## 5. Landmark Analysis

```stata
// สำหรับ time-varying treatment หรือ immortal time bias correction

// เลือกผู้ป่วยที่รอดถึง landmark time (e.g., 12 months)
stset time, failure(event==1) id(id)

// Landmarkไ method: restrict + reset time
preserve
    keep if time > 12              // รอดถึง landmark
    replace time = time - 12       // re-zero from landmark
    replace _t  = _t  - 12
    stcox i.treatment age bmi i.sex, vce(robust)
    sts graph, by(treatment) scheme(s2color) ///
        title("Landmark Analysis at 12 months")
    graph export "fig_landmark.tif", as(tif) replace width(3000)
restore
```

## 6. Time-varying Covariates

```stata
// สำหรับ treatment ที่เปลี่ยนแปลงตาม follow-up time

// ต้องสร้าง long format: 1 row per interval per person
// id, tstart, tstop, event, treatment_tv

stset tstop, failure(event==1) id(id) enter(tstart)
stcox treatment_tv age bmi i.sex, vce(robust)
```

## 7. Incidence Rate

```stata
// Person-time analysis
stset futime, failure(event==1) id(id) scale(365.25)
stptime, by(treatment) per(1000)
// Output: Events, Person-years, Incidence Rate, 95% CI (exact Poisson)

// Incidence Rate Ratio
stptime, by(treatment) per(1000) dd(2)
// Rate ratio = IR_treatment / IR_control
```

## 8. Forest Plot สำหรับ Subgroup Analysis

```stata
// ─── เตรียมข้อมูล subgroup HRs ───────────────────────────────────
// วิเคราะห์ Cox แต่ละ subgroup แล้วรวม HR เป็น dataset

preserve
    clear
    input str25 subgroup hr ll ul pinteract
    "Overall"          0.72 0.58 0.89 .
    "Age < 65"         0.68 0.50 0.92 0.54
    "Age ≥ 65"         0.76 0.55 1.04 .
    "Male"             0.70 0.52 0.93 0.78
    "Female"           0.74 0.53 1.04 .
    "No diabetes"      0.65 0.48 0.88 0.42
    "Diabetes"         0.82 0.55 1.22 .
    end

    // Forest plot (metan)
    ssc install metan, replace
    metan hr ll ul, lcols(subgroup)        ///
        effect("Hazard Ratio")              ///
        null(1) xlog                        ///
        diamond(1)                          ///
        scheme(s2color)                     ///
        title("Subgroup Analysis — Primary Endpoint")
    graph export "fig_forest_subgroup.tif", as(tif) replace width(3000)
restore
```

## 9. Joint Models (Longitudinal + Survival)

```stata
// สำหรับ longitudinal biomarker + time-to-event
// STATA 18: ใช้ stjm package
ssc install stjm, replace

// Longitudinal submodel: mixed effects
// Survival submodel: Cox

stjm y treatment time,                              ///
    survmodel(cox)                                   ///
    longitudinal(mixed)                              ///
    timevar(time)                                    ///
    failure(event)                                   ///
    id(id)
// Reports: association parameter (α) linking biomarker trajectory to hazard
```

## 10. Results Reporting Template

### Methods Section

```
Survival curves were estimated using the Kaplan-Meier method and compared
using the log-rank test. Hazard ratios (HRs) with 95% confidence intervals
(CIs) were estimated using Cox proportional hazards regression. The
proportional hazards assumption was verified using scaled Schoenfeld residuals
(estat phtest) and log-log plots. Multivariable models were adjusted for
[covariates]. Where the proportional hazards assumption was violated,
[stratified Cox / time-varying coefficient / Royston-Parmar model] was used.
For outcomes subject to competing risks, the Fine-Gray subdistribution hazard
model was applied. All tests were two-sided with α = 0.05. Statistical
analyses were performed using STATA version 18 (StataCorp, College Station,
TX, USA).
```

### Results Section Template

```
Median overall survival was X.X months (95% CI Y.Y–Z.Z) in the treatment
group vs. X.X months (95% CI Y.Y–Z.Z) in the control group (log-rank
p = 0.xxx). The 1-, 3-, and 5-year OS rates were X%, X%, and X% in the
treatment group vs. X%, X%, and X% in the control group, respectively.

On multivariable Cox regression, treatment was independently associated
with improved OS (HR 0.XX, 95% CI 0.XX–0.XX, p = 0.xxx), after adjusting
for age, sex, and disease stage. The proportional hazards assumption was
satisfied for all covariates (global Schoenfeld test p = 0.XX).
```
