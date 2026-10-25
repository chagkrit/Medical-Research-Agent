# Inferential Statistics — Medical Research Reference (STATA 18)
# สถิติเชิงอนุมานสำหรับงานวิจัยทางการแพทย์

> **ทุก code ในไฟล์นี้ใช้ STATA 18** — เริ่มทุก do-file ด้วย `version 18` + `set scheme s2color`

## 1. แผนผังเลือกวิธีทดสอบ

```
ตัวแปร outcome เป็น continuous?
├── YES → กลุ่มเปรียบเทียบกี่กลุ่ม?
│   ├── 2 กลุ่ม → ตรวจ normality (swilk / sktest)
│   │   ├── Normal → t-test independent หรือ paired t-test
│   │   └── Non-normal → ranksum (Mann-Whitney U)
│   └── ≥ 3 กลุ่ม → ตรวจ normality
│       ├── Normal → oneway ANOVA → post-hoc (pwmean, Tukey)
│       └── Non-normal → kwallis → post-hoc (Dunn via nptrend)
└── NO (categorical) → ตรวจ cell frequencies
    ├── Expected ≥ 5 ทุก cell → Chi-square (tabulate, chi2)
    └── Expected < 5 → Fisher's exact (tabulate, exact)
```

## 2. Parametric Tests

### Normality Check (เสมอก่อน parametric test)

```stata
version 18
set more off

// Shapiro-Wilk (แนะนำสำหรับ n < 2000)
swilk age bmi creatinine
// H0: ข้อมูลมีการกระจายแบบ normal; p > 0.05 = ผ่าน

// Skewness-Kurtosis (n ≥ 2000)
sktest age bmi creatinine

// QQ plot (visual)
qnorm age, scheme(s2color) title("Normal Q-Q Plot: Age")
graph export "qnorm_age.tif", as(tif) replace width(3000)
```

### Independent Samples t-test

```stata
// ตรวจ equal variance ก่อน
robvar outcome, by(group)    // Brown-Forsythe test
// p > 0.05 = equal variances

// t-test (equal variance)
ttest outcome, by(group)

// Welch's t-test (unequal variance — safer default)
ttest outcome, by(group) unequal

// Effect size: Cohen's d
// d = (mean1 - mean2) / pooled SD
quietly ttest outcome, by(group)
scalar cohen_d = (r(mu_1) - r(mu_2)) / r(sd)
di "Cohen's d = " cohen_d
```

**Reporting:** t([df]) = [t], p = [p], Cohen's d = [d] (95% CI [lower]–[upper])

### Paired t-test

```stata
// Before-after, matched pairs
ttest pre == post
// หรือ: gen diff = post - pre; ttest diff == 0

// Effect size
quietly ttest pre == post
scalar cohen_d_paired = r(mu_1) - r(mu_2)) / r(sd)
```

**ใช้เมื่อ:** before-after, matched pairs

### One-way ANOVA

```stata
// Parametric
oneway outcome group, tabulate    // F-test + group means

// Post-hoc: Tukey HSD (แนะนำสำหรับ Q1)
pwmean outcome, over(group) mcompare(tukey) effects

// Effect size: η² (eta squared) = SS_between / SS_total
anova outcome i.group
scalar eta_sq = e(SS_model) / (e(SS_model) + e(SS_residual))
di "η² = " eta_sq

// Levene's test สำหรับ equal variance
robvar outcome, by(group)
```

### Repeated Measures ANOVA

```stata
// Sphericity assumption → xtmixed / mixed command
mixed outcome time || id:, reml
// หรือ
anova outcome id time, repeated(time)

// Post-hoc สำหรับ repeated measures
pwmean outcome, over(time) mcompare(bonferroni) effects
```

## 3. Non-Parametric Tests

### Mann-Whitney U (Wilcoxon Rank-Sum)

```stata
// Two independent groups, non-normal continuous outcome
ranksum outcome, by(group)

// Effect size: r = Z / sqrt(N)
quietly ranksum outcome, by(group)
scalar z_mw = r(z)
scalar n_total = r(N_1) + r(N_2)
scalar r_effect = z_mw / sqrt(n_total)
di "Effect size r = " r_effect
// 0.1 = small, 0.3 = medium, 0.5 = large (Cohen)
```

### Kruskal-Wallis

```stata
// ≥ 3 groups, non-normal continuous
kwallis outcome, by(group)

// Post-hoc: Dunn test (Bonferroni-corrected pairwise comparisons)
ssc install dunntest, replace
dunntest outcome, by(group) bonferroni
```

### Wilcoxon Signed-Rank (paired, non-normal)

```stata
signrank pre = post
// หรือ: gen diff = post - pre; signrank diff == 0
```

## 4. Categorical Analysis

### Chi-square Test

```stata
// 2×2 หรือ r×c contingency table
tabulate group outcome, chi2 exact row col
// chi2: Chi-square statistic + p
// exact: Fisher's exact (compute ไปพร้อมกัน)
// row/col: แสดง row/column percentages

// ตรวจ expected frequencies (ต้อง ≥ 5 ทุก cell สำหรับ chi2)
tabulate group outcome, expected

// Yates correction สำหรับ 2×2 (อัตโนมัติใน tabulate)
tabulate group outcome, chi2 yates

// Effect size: Cramér's V
ssc install cramersv, replace
cramersv group outcome
// หรือ manual: V = sqrt(chi2 / (n * (min(rows,cols) - 1)))
```

### Fisher's Exact Test

```stata
// ใช้เมื่อ expected cell frequency < 5
tabulate group outcome, exact
// รายงาน: p (two-sided, exact)
```

## 5. Regression Analysis

### Simple Logistic Regression

```stata
// Unadjusted OR
logistic outcome predictor
// Output: OR (directly) + 95% CI + p
// ถ้าต้องการ coef: logit outcome predictor

// Continuous predictor: OR per 1-unit increase
logistic outcome age

// Categorical predictor
logistic outcome i.sex    // i. = factor variable (baseline = first group)

// Odds Ratio table
logistic outcome age i.sex bmi
```

### Multiple Logistic Regression

```stata
// Step 1: Univariable screening (report ทุกตัว)
foreach v of varlist age bmi i.sex i.diabetes i.smoking i.hypertension {
    quietly logistic outcome `v'
    eststo uni_`v'
}
esttab uni_*, eform ci(3) p(3) label

// Step 2: Multivariable (confounders pre-specified, ไม่ใช้ stepwise)
logistic outcome age bmi i.sex i.diabetes i.smoking
eststo multi_adjusted

// Step 3: Model diagnostics
estat gof, group(10)           // Hosmer-Lemeshow test (p > 0.05 = good fit)
lroc                           // ROC curve + AUC
predict pred_prob, pr
estat classification            // sensitivity, specificity at optimal cutpoint

// VIF (multicollinearity)
logit outcome age bmi i.sex i.diabetes i.smoking    // use logit (not logistic) for vif
vif
// VIF > 5 = concern; VIF > 10 = serious problem

// Pseudo R² (McFadden)
quietly logistic outcome age bmi i.sex i.diabetes
scalar pseudo_r2 = 1 - e(ll) / e(ll_0)
di "McFadden Pseudo R² = " pseudo_r2

// Export results
esttab uni_* multi_adjusted using "table_logistic.xlsx",  ///
    eform ci(3) p(3) label replace excel                   ///
    title("Logistic Regression Analysis")
```

### Linear Regression

```stata
// Simple
regress outcome predictor

// Multivariable
regress outcome age bmi i.sex i.diabetes, vce(robust)
// vce(robust): heteroscedasticity-robust SE

// Model diagnostics
rvfplot, yline(0) scheme(s2color)          // Residual vs Fitted
graph export "fig_rvf.tif", as(tif) replace width(3000)

qnorm e, scheme(s2color)                  // QQ plot of residuals
graph export "fig_residual_qq.tif", as(tif) replace width(3000)

// Assumptions
estat imtest, white          // White test for heteroscedasticity
estat ovtest               // Ramsey RESET for omitted variables
vif                          // multicollinearity

// Standardized beta coefficients
regress outcome age bmi, beta
// Output: std beta in column [Beta]
```

## 6. Correlation

```stata
// Pearson (continuous, normal)
correlate x y
// หรือ:
pwcorr x y, sig star(0.05)

// Spearman (non-normal, ordinal)
spearman x y

// Kendall's tau (small n or many ties)
ktau x y

// Interpretation (Cohen's guideline):
// |r| < 0.1: negligible
// 0.1–0.3: small
// 0.3–0.5: moderate
// > 0.5: large

// Correlation matrix
pwcorr age bmi creatinine egfr, sig star(0.05) obs
```

## 7. Multiple Testing Correction

```stata
// ─── Bonferroni ──────────────────────────────────────────────────
// Manual: adjusted alpha = 0.05 / n_tests
local n_tests = 3
local alpha_bonf = 0.05 / `n_tests'
di "Bonferroni-adjusted alpha = " `alpha_bonf'

// ─── Holm-Bonferroni (step-down, more power) ──────────────────────
ssc install holm, replace
// รวบรวม p-values ก่อน
matrix p_vals = (0.01, 0.04, 0.03, 0.001, 0.5)
holm p_vals, method(holm)

// ─── FDR (Benjamini-Hochberg) — สำหรับ exploratory ───────────────
ssc install bh, replace     // Benjamini-Hochberg
// หรือ package: fdr
```

## 8. Sample Size Calculation

```stata
// ─── Two-sample t-test ────────────────────────────────────────────
power twomeans 0 0.5, sd(1) alpha(0.05) power(0.80)
// หรือ: ระบุ sd ต่างกัน
power twomeans 0 0.5, sd1(1) sd2(1.2) alpha(0.05) power(0.80)

// ─── Chi-square test (two proportions) ────────────────────────────
power twoproportions 0.20 0.35, alpha(0.05) power(0.80)

// ─── Logistic regression ──────────────────────────────────────────
// ใช้ G*Power ร่วมกับ STATA สำหรับ complex logistic sample size
// หรือ: power onemean / twomeans ใกล้เคียง

// ─── Summary table ────────────────────────────────────────────────
power twomeans (0(0.1)0.5), sd(1) alpha(0.05) power(0.80) table
// แสดง n ที่ต้องการสำหรับ effect sizes ต่างๆ
```

## 9. Results Table Template (Publication-ready)

```
Table 2. Multivariable Logistic Regression Analysis

Variable              | Crude OR  | 95% CI       | p      | Adjusted OR | 95% CI       | p
----------------------|-----------|--------------|--------|-------------|--------------|--------
Age (per year)        | 1.03      | 1.01–1.05    | 0.008  | 1.03        | 1.01–1.06    | 0.012
Male sex              | 1.58      | 1.11–2.25    | 0.012  | 1.57        | 1.10–2.24    | 0.014
Diabetes mellitus     | 2.43      | 1.58–3.73    | <0.001 | 2.41        | 1.56–3.71    | <0.001
BMI (per kg/m²)       | 1.04      | 1.00–1.08    | 0.046  | 1.04        | 1.001–1.08   | 0.048

Hosmer-Lemeshow: χ²(8) = 6.24, p = 0.62
Nagelkerke R² = 0.18
C-statistic (AUC) = 0.74 (95% CI 0.68–0.80)

Statistical analysis: STATA version 18 (StataCorp, College Station, TX, USA)
```

## 10. Methods Section Template

```
All statistical analyses were performed using STATA version 18
(StataCorp, College Station, TX, USA). Continuous variables were
presented as mean ± SD or median (interquartile range) based on
normality assessed by the Shapiro-Wilk test. Categorical variables
were presented as frequency (percentage). Between-group comparisons
for continuous variables were performed using the independent t-test
or Mann-Whitney U test as appropriate. Chi-square or Fisher's exact
test was used for categorical variables. Multivariable logistic
regression was used to identify independent predictors, with results
expressed as odds ratios (OR) with 95% confidence intervals (CI).
Model calibration was assessed using the Hosmer-Lemeshow goodness-of-fit
test. All tests were two-sided with a significance level of 0.05.
```
