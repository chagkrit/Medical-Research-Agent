# STATA 18 Figure Guide for Medical Research
# คู่มือสร้าง Figure ด้วย STATA 18 สำหรับ publication-ready output

> **หลักการ**: ทุก figure สร้างด้วย **STATA 18 graph commands** เท่านั้น
> ใช้ `set scheme s2color` เป็นค่าเริ่มต้นทุกครั้ง
> Export ทุก figure ด้วย `graph export "file.tif", as(tif) replace width(3000)`

---

## Standard Setup (เริ่มทุก do-file)

```stata
version 18
set more off
set scheme s2color       // STATA default — navy/maroon palette, white background

// ─── Global figure export settings ───────────────────────────────
// width(3000) = 3000 pixels wide
// ถ้า figure ≈ 10 inches wide → 300 DPI (publication-ready)
// ถ้าต้องการ 600 DPI → width(6000)
```

---

## STATA s2color Palette

STATA s2color scheme มีสีมาตรฐานดังนี้ (ใช้อ้างอิงใน graph options):

| ชื่อสี | STATA name | Hex | ใช้สำหรับ |
|---|---|---|---|
| Navy | `navy` | #1a476f | กลุ่ม 1 / primary line |
| Maroon | `maroon` | #90353b | กลุ่ม 2 / comparison |
| Forest green | `forest_green` | #55752f | กลุ่ม 3 |
| Dull teal | `dull_teal` | #4a8099 | กลุ่ม 4 |
| Cranberry | `cranberry` | #8b0d55 | highlight / significant |
| Gray | `gs8` | #808080 | reference lines, censoring |

---

## 1. Kaplan-Meier Survival Curve

```stata
// ─── Setup survival data ──────────────────────────────────────────
stset time, failure(event==1) id(id)

// ─── KM curve (2 groups) — Q1 standard ───────────────────────────
sts graph, by(treatment)                         ///
    scheme(s2color)                               ///
    ci                                            ///   95% CI bands
    risktable(,                                   ///   at-risk table (MANDATORY Q1)
              order(1 "Control" 2 "Treatment")    ///
              title("No. at risk", size(small)))  ///
    plot1opts(lwidth(medthick) lcolor(navy))      ///   group 1 style
    plot2opts(lwidth(medthick) lcolor(maroon))    ///   group 2 style
    ciopts(lwidth(vthin))                         ///
    title("Overall Survival", size(medium))       ///
    ytitle("Survival Probability")                ///
    xtitle("Time (months)")                       ///
    ylabel(0(0.2)1, format(%3.1f) grid)           ///
    xlabel(0(12)60, grid)                         ///
    legend(order(1 "Control" 3 "Treatment")       ///
           ring(0) pos(1) cols(1))

// Add log-rank p-value annotation
sts test treatment
local p = r(p)
if `p' < 0.001 local p_text "p < 0.001"
else local p_text : display "p = " %5.3f `p'
// Note: ใส่ note() หรือ text() ใน sts graph option หรือ graph di ตามหลัง

graph export "fig1_km.tif", as(tif) replace width(3000)

// ─── KM ≥ 3 groups ────────────────────────────────────────────────
sts graph, by(stage)                             ///
    scheme(s2color) ci risktable                  ///
    plot1opts(lcolor(navy))                       ///
    plot2opts(lcolor(maroon))                     ///
    plot3opts(lcolor(forest_green))               ///
    legend(order(1 "Stage I" 3 "Stage II" 5 "Stage III") ring(0) pos(1))
graph export "fig_km_3groups.tif", as(tif) replace width(3000)
```

---

## 2. ROC Curve

```stata
// ─── Single ROC (after logistic) ─────────────────────────────────
logistic disease age bmi score1 score2
lroc, scheme(s2color)                    ///
    title("ROC Curve: Prediction Model") ///
    xtitle("1 − Specificity")            ///
    ytitle("Sensitivity")
graph export "fig_roc.tif", as(tif) replace width(3000)

// ─── ROC from continuous score ────────────────────────────────────
roctab disease score, graph nograph summary
roctab disease score, graph scheme(s2color) title("ROC Curve")
graph export "fig_roc_score.tif", as(tif) replace width(3000)

// ─── Comparison of two ROC curves (DeLong test) ───────────────────
roccomp disease score1 score2,            ///
    graph scheme(s2color)                  ///
    title("ROC Comparison")               ///
    xtitle("1 − Specificity")             ///
    ytitle("Sensitivity")                  ///
    legend(order(1 "Clinical Score" 2 "Biomarker Model") ///
           ring(0) pos(4))
graph export "fig_roc_compare.tif", as(tif) replace width(3000)
```

---

## 3. Forest Plot

```stata
// ─── Install metan ────────────────────────────────────────────────
ssc install metan, replace

// ─── Subgroup analysis forest plot ───────────────────────────────
// เตรียมข้อมูล: study, hr, ll, ul (hazard ratio + 95% CI)
input str25 subgroup n_sub hr ll ul p_interact
"Overall"       450  0.72  0.58  0.89  .
"  Age < 65"    210  0.68  0.50  0.92  0.54
"  Age ≥ 65"    240  0.76  0.55  1.04  .
"  Male"        260  0.70  0.52  0.93  0.78
"  Female"      190  0.74  0.53  1.04  .
"  No diabetes" 310  0.65  0.48  0.88  0.42
"  Diabetes"    140  0.82  0.55  1.22  .
end

metan hr ll ul,                           ///
    lcols(subgroup n_sub)                  ///   left columns in table
    effect("Hazard Ratio")                 ///
    null(1)                                ///   null value = 1 for ratio
    xlog                                   ///   log scale for x-axis
    diamond(1)                             ///   diamond for overall
    scheme(s2color)                        ///
    title("Subgroup Analysis — Primary Endpoint", size(medium)) ///
    xlabel(0.3 0.5 1 2, grid)
graph export "fig_forest_subgroup.tif", as(tif) replace width(3000)

// ─── Meta-analysis forest plot ────────────────────────────────────
metan or ll ul, random                    ///
    lcols(author year n_total)             ///
    effect("Odds Ratio")                   ///
    null(1) xlog                           ///
    diamond(1)                             ///
    scheme(s2color)                        ///
    title("Meta-analysis: [Outcome]", size(medium))
graph export "fig_forest_meta.tif", as(tif) replace width(3000)
```

---

## 4. Calibration Plot

```stata
// ─── pmcalplot (recommended for Q1) ──────────────────────────────
ssc install pmcalplot, replace

logistic disease age bmi score1 score2
predict pred_prob, pr

pmcalplot pred_prob disease,              ///
    ci                                     ///   95% CI around calibration curve
    cuts(10)                               ///   10 equally-spaced groups
    scheme(s2color)                        ///
    title("Calibration Plot", size(medium)) ///
    xtitle("Predicted Probability")        ///
    ytitle("Observed Proportion")          ///
    xline(0 1, lcolor(gs10))              ///
    yline(0 1, lcolor(gs10))
graph export "fig_calibration.tif", as(tif) replace width(3000)

// ─── Manual calibration plot ──────────────────────────────────────
xtile prob_decile = pred_prob, nq(10)
bysort prob_decile: egen obs_mean = mean(disease)
bysort prob_decile: egen pred_mean = mean(pred_prob)

twoway (scatter obs_mean pred_mean, mcolor(navy) msymbol(O) msize(medium)) ///
       (rcap ci_low ci_high pred_mean, lcolor(navy) lwidth(thin))          ///
       (line pred_mean pred_mean, lcolor(gs8) lwidth(thin) lpattern(dash)) ///
    , scheme(s2color)                                                        ///
    title("Calibration Plot") xtitle("Predicted") ytitle("Observed")         ///
    xlabel(0(.2)1) ylabel(0(.2)1) aspectratio(1)
graph export "fig_calibration_manual.tif", as(tif) replace width(3000)
```

---

## 5. Decision Curve Analysis

```stata
// ─── DCA (ssc install dca) ───────────────────────────────────────
ssc install dca, replace

predict pred_prob, pr

dca disease pred_prob,                    ///
    smooth                                 ///
    xrange(0.05 0.50)                     ///   threshold range
    scheme(s2color)                        ///
    title("Decision Curve Analysis", size(medium)) ///
    ytitle("Net Benefit")                  ///
    xtitle("Threshold Probability")        ///
    legend(order(1 "Treat None" 2 "Treat All" 3 "Model") ///
           ring(0) pos(3) cols(1))
graph export "fig_dca.tif", as(tif) replace width(3000)
```

---

## 6. Love Plot (Covariate Balance)

```stata
// ─── Love Plot: SMD ก่อนและหลัง balancing ────────────────────────
// เตรียมข้อมูล SMD (จาก stddiff หรือ tebalance)
preserve
    clear
    input str25 varname smd_b smd_a
    "Age"              0.45  0.03
    "BMI"              0.38  0.05
    "Male sex"         0.52  0.04
    "Diabetes"         0.41  0.06
    "Smoking"          0.33  0.08
    "Hypertension"     0.28  0.07
    "Creatinine"       0.35  0.09
    end
    
    gen y = _n
    local n = _N
    
    twoway (scatter y smd_b, msymbol(Oh) mcolor(maroon) msize(medlarge)) ///
           (scatter y smd_a, msymbol(O)  mcolor(navy)   msize(medlarge)) ///
           (pcspike y smd_b y smd_a, lcolor(gs10) lwidth(thin)),         ///
        xline(0,   lcolor(black) lwidth(thin))                            ///
        xline(0.1, lcolor(black) lwidth(thin) lpattern(dash))            ///
        xlabel(0(.1).6, grid)                                             ///
        ylabel(1/`n', valuelabel angle(0))                                ///
        ytitle("") xtitle("Standardized Mean Difference (SMD)")           ///
        legend(order(1 "Before Balancing" 2 "After Balancing")            ///
               ring(0) pos(4) cols(1))                                    ///
        title("Love Plot: Covariate Balance", size(medium))               ///
        scheme(s2color)
    
    label define y_lbl 1 "Age" 2 "BMI" 3 "Male sex" 4 "Diabetes" ///
                       5 "Smoking" 6 "Hypertension" 7 "Creatinine"
    label values y y_lbl
    
    graph export "fig_love.tif", as(tif) replace width(3000)
restore
```

---

## 7. Funnel Plot (Meta-analysis)

```stata
// ─── Install metafunnel ───────────────────────────────────────────
ssc install metafunnel, replace

// log OR + SE(log OR)
gen log_or = ln(or)
gen se_log_or = (ln(ul) - ln(ll)) / (2 * 1.96)

metafunnel log_or se_log_or,             ///
    scheme(s2color)                       ///
    title("Funnel Plot", size(medium))    ///
    xtitle("Log Odds Ratio")              ///
    ytitle("Standard Error")
graph export "fig_funnel.tif", as(tif) replace width(3000)

// ─── Egger's test ────────────────────────────────────────────────
metabias log_or se_log_or, egger
// p < 0.05 = evidence of publication bias (asymmetric funnel)
```

---

## 8. Bland-Altman Plot

```stata
// ─── ติดตั้ง ──────────────────────────────────────────────────────
ssc install batplot, replace

// Method 1: batplot (user-written)
batplot method1 method2, scheme(s2color)   ///
    title("Bland-Altman Plot", size(medium)) ///
    xtitle("Mean of Two Methods")            ///
    ytitle("Difference (Method 1 − Method 2)")
graph export "fig_ba.tif", as(tif) replace width(3000)

// Method 2: Manual
gen ba_mean = (method1 + method2) / 2
gen ba_diff = method1 - method2
summarize ba_diff
local mean_diff = r(mean)
local sd_diff   = r(sd)
local loa_upper = `mean_diff' + 1.96*`sd_diff'
local loa_lower = `mean_diff' - 1.96*`sd_diff'

twoway (scatter ba_diff ba_mean, mcolor(navy) msymbol(O) msize(small))   ///
       (lowess ba_diff ba_mean, lcolor(maroon) lwidth(medium)),           ///
    yline(`mean_diff', lcolor(black) lwidth(medium))                       ///
    yline(`loa_upper' `loa_lower', lcolor(gs8) lpattern(dash))            ///
    scheme(s2color)                                                         ///
    title("Bland-Altman Plot") xtitle("Mean") ytitle("Difference")
graph export "fig_ba_manual.tif", as(tif) replace width(3000)
```

---

## 9. Bar Chart

```stata
// ─── Simple bar chart ─────────────────────────────────────────────
graph bar (mean) outcome, over(group, label(angle(45))) ///
    scheme(s2color)                                       ///
    title("Outcome by Group") ytitle("Mean ± SE")         ///
    bar(1, color(navy)) bar(2, color(maroon))             ///
    ci(var)   // error bars

// ─── Grouped bar chart ────────────────────────────────────────────
graph bar (mean) value1 value2 value3, over(group)  ///
    scheme(s2color)                                   ///
    title("Grouped Bar Chart")                        ///
    legend(order(1 "Endpoint 1" 2 "Endpoint 2" 3 "Endpoint 3"))

graph export "fig_bar.tif", as(tif) replace width(3000)
```

---

## 10. Box Plot

```stata
// ─── Box plot (STATA native) ──────────────────────────────────────
graph box outcome, over(group)               ///
    scheme(s2color)                           ///
    title("Distribution by Group")            ///
    ytitle("Outcome Measure")                 ///
    box(1, color(navy))                       ///
    box(2, color(maroon))                     ///
    medtype(line) medline(lcolor(white) lwidth(medium))

graph export "fig_box.tif", as(tif) replace width(3000)

// ─── Box + jitter (individual points) ────────────────────────────
// STATA 18: graph box + overlay scatter
graph box outcome, over(group) scheme(s2color)
// ใส่ jitter ผ่าน twoway instead:
twoway (scatter outcome group if group==1, jitter(3) mcolor(navy%50)   ///
            msymbol(O) msize(small))                                    ///
       (scatter outcome group if group==2, jitter(3) mcolor(maroon%50) ///
            msymbol(O) msize(small)),                                   ///
    scheme(s2color) title("Box + Individual Points")
graph export "fig_box_jitter.tif", as(tif) replace width(3000)
```

---

## 11. Scatter Plot with Regression Line

```stata
// ─── Scatter + lowess ─────────────────────────────────────────────
twoway (scatter y x, mcolor(navy) msymbol(O) msize(small) alpha(0.7)) ///
       (lfit y x, lcolor(maroon) lwidth(medium))                       ///
       (lfitci y x, lcolor(maroon%30) fcolor(maroon%20)),              ///
    scheme(s2color)                                                      ///
    title("Scatter Plot with Regression Line")                           ///
    xtitle("X Variable") ytitle("Y Variable")

// Add R² annotation
quietly regress y x
local r2 = string(e(r2), "%5.3f")
local p  = string(2*ttail(e(df_r), abs(_b[x]/_se[x])), "%5.3f")
note: "R² = `r2', p = `p'"

graph export "fig_scatter.tif", as(tif) replace width(3000)
```

---

## Quick Reference: Export Standards

```stata
// ─── Publication-ready export (ALL figures) ──────────────────────
// TIFF at ≥300 DPI (width(3000) = 300 DPI for 10-inch figure)
graph export "fig_name.tif", as(tif) replace width(3000)

// PNG (ถ้า journal ต้องการ PNG)
graph export "fig_name.png", as(png) replace width(3000)

// EPS (vector, for journals requiring EPS)
graph export "fig_name.eps", as(eps) replace

// ─── Batch export หลายรูปพร้อมกัน ────────────────────────────────
// ใช้ graph save แล้ว combine / export ทีหลัง
graph save "fig1.gph", replace
graph save "fig2.gph", replace
graph combine "fig1.gph" "fig2.gph", scheme(s2color) cols(2)
graph export "fig_combined.tif", as(tif) replace width(6000)
```

## Required SSC Packages

```stata
// ติดตั้ง packages สำหรับ medical research figures
ssc install metan,       replace   // forest plot
ssc install metafunnel,  replace   // funnel plot
ssc install pmcalplot,   replace   // calibration plot
ssc install dca,         replace   // decision curve analysis
ssc install batplot,     replace   // Bland-Altman plot
ssc install stddiff,     replace   // standardized mean difference
ssc install table1_mc,  replace   // Table 1
ssc install psmatch2,    replace   // propensity score matching
ssc install ebalance,    replace   // entropy balancing
ssc install evalue,      replace   // E-value
```
