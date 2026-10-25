# Diagnostic Accuracy Analysis — STARD Compliant (STATA 18)
# การวิเคราะห์ความแม่นยำในการวินิจฉัย

> **ทุก code ในไฟล์นี้ใช้ STATA 18** — เริ่มทุก do-file ด้วย `version 18` + `set scheme s2color`

## 1. Core Metrics (Sensitivity, Specificity, PPV, NPV)

```stata
version 18
set more off
set scheme s2color

// ─── 2×2 Table Analysis (at chosen cutpoint) ─────────────────────
// disease: 1=positive, 0=negative (reference standard)
// test_pos: 1=test positive, 0=test negative (index test)
diagt disease test_pos
// Output: Sensitivity, Specificity, PPV, NPV, LR+, LR-, DOR + 95% CI

// ─── Wilson 95% CI for individual metrics ─────────────────────────
// Sensitivity = TP/(TP+FN): 150 TP out of 175 positives
cii 175 150, wilson

// Specificity = TN/(TN+FP): 95 TN out of 110 negatives
cii 110 95, wilson

// ─── Full contingency table ────────────────────────────────────────
tabulate disease test_pos, row col exact
```

## 2. ROC Curve — Publication Quality

```stata
// ─── ROC after logistic model ─────────────────────────────────────
logistic disease age bmi score1 score2
predict pred_prob, pr
lroc, scheme(s2color)                    ///
    title("ROC Curve: Prediction Model")
graph export "fig_roc.tif", as(tif) replace width(3000)

// ─── ROC from continuous score directly ───────────────────────────
roctab disease score, summary graph     ///
    scheme(s2color)                      ///
    title("ROC Curve: Biomarker Score")
graph export "fig_roc_score.tif", as(tif) replace width(3000)
// Output: AUC + 95% CI + SE

// ─── Youden optimal cutpoint ─────────────────────────────────────
roctab disease score, detail
// Column "Youden" = sensitivity + specificity - 1
// Row with max Youden = optimal cutpoint

// ─── Cross-validation (leave-one-out) ────────────────────────────
rocgold disease score, detail
```

## 3. DeLong Test — Comparing Two AUCs

```stata
// ─── Compare two ROC curves (paired design) ──────────────────────
// score1, score2: predictions from two models, same patients
roccomp disease score1 score2,           ///
    graph scheme(s2color)                  ///
    title("ROC Comparison: Model 1 vs Model 2") ///
    legend(order(1 "Model 1" 2 "Model 2") ring(0) pos(4))
graph export "fig_roc_compare.tif", as(tif) replace width(3000)
// Output: AUC1, AUC2, chi2, p (DeLong test)

// ─── Unpaired ROC comparison ─────────────────────────────────────
roccomp disease score1 score2, unpaired graph
```

## 4. Calibration Assessment

```stata
// ─── Hosmer-Lemeshow Goodness-of-Fit ─────────────────────────────
logistic disease age bmi score1 score2
estat gof, group(10)
// H0: calibration adequate; p > 0.05 = good calibration
// Report: χ²(8) = [value], p = [value]

// ─── Calibration Plot (publication-quality) ───────────────────────
ssc install pmcalplot, replace
predict pred_prob, pr
pmcalplot pred_prob disease,              ///
    ci                                     ///
    title("Calibration Plot")             ///
    scheme(s2color)                        ///
    xtitle("Predicted Probability")        ///
    ytitle("Observed Proportion")
graph export "fig_calibration.tif", as(tif) replace width(3000)
// Shows: observed vs predicted with CI + ideal line

// ─── Calibration slope + intercept ───────────────────────────────
// Calibration-in-the-large (intercept = 0; slope = 1 = perfect)
logit pred_logit = logit(pred_prob)
regress disease pred_logit
// Intercept ≈ 0 and slope ≈ 1 = excellent calibration

// ─── Brier Score ─────────────────────────────────────────────────
gen brier_i = (disease - pred_prob)^2
summarize brier_i
di "Brier Score = " r(mean)
// < 0.25 = better than chance; lower = better
```

## 5. Decision Curve Analysis (DCA)

```stata
// ─── DCA (user-written command) ───────────────────────────────────
ssc install dca, replace

predict pred_prob, pr

// Basic DCA
dca disease pred_prob,                    ///
    smooth                                 ///
    title("Decision Curve Analysis")       ///
    scheme(s2color)                        ///
    ytitle("Net Benefit")                  ///
    xtitle("Threshold Probability")
graph export "fig_dca.tif", as(tif) replace width(3000)

// ─── DCA comparing multiple models ───────────────────────────────
// Predict from each model first
logistic disease score1
predict prob_model1, pr
logistic disease score1 score2 age bmi
predict prob_model2, pr

dca disease prob_model1 prob_model2,      ///
    smooth                                 ///
    xrange(0.05 0.35)                     ///
    title("Decision Curve Analysis: Model Comparison") ///
    legend(order(1 "Treat None" 2 "Treat All" ///
                 3 "Clinical Score" 4 "Full Model") ring(0)) ///
    scheme(s2color)
graph export "fig_dca_compare.tif", as(tif) replace width(3000)
```

## 6. Net Reclassification Improvement (NRI) and IDI

```stata
// ─── NRI: ประเมิน improvement เมื่อเพิ่ม biomarker ──────────────
ssc install nri_ipcw, replace    // สำหรับ censored survival outcomes
// หรือ ใช้ manual NRI สำหรับ binary outcome

// Manual NRI (binary outcome, two risk categories)
// Category: < 0.20 = low risk; ≥ 0.20 = high risk
gen riskcat_m1 = (prob_model1 >= 0.20)
gen riskcat_m2 = (prob_model2 >= 0.20)

// NRI for events
count if disease==1 & riskcat_m2 > riskcat_m1   // correctly reclassified up
count if disease==1 & riskcat_m2 < riskcat_m1   // incorrectly reclassified down

// NRI for non-events
count if disease==0 & riskcat_m2 < riskcat_m1   // correctly reclassified down
count if disease==0 & riskcat_m2 > riskcat_m1   // incorrectly reclassified up

// Continuous NRI (no categories)
// NRI+ = P(↑ | event) − P(↑ | non-event)
// IDI = Integrated Discrimination Improvement
gen idi_i = (prob_model2 - prob_model1) * (disease==1) - ///
            (prob_model2 - prob_model1) * (disease==0)
summarize idi_i
di "IDI = " r(mean)
```

## 7. Multiple Cutpoints (Sensitivity Table)

```stata
// ─── Roctab detail: สร้าง table ทุก cutpoint ────────────────────
roctab disease score, detail nograph
// เลือก cutpoint จาก: max Youden / max Sn ที่ Sp ≥ 90% / clinical threshold

// ─── Predefined cutpoints ─────────────────────────────────────────
foreach cut in 0.2 0.3 0.4 0.5 {
    gen test_cut`cut'100 = (pred_prob >= `cut')
    diagt disease test_cut`cut'100
    drop test_cut`cut'100
}
```

## 8. Results Reporting Template (STARD 2015)

```
Diagnostic Accuracy Results:
"The [index test] was evaluated against [reference standard] in [n] participants
([n1] with the condition, [n0] without).

The area under the receiver operating characteristic curve (AUROC) was 0.XX
(95% CI 0.XX–0.XX). At the optimal cutpoint of [value] (Youden index = [value]),
the sensitivity was XX% (95% CI XX–XX%) and specificity was XX% (95% CI XX–XX%).
The positive predictive value was XX% (95% CI XX–XX%) and negative predictive
value was XX% (95% CI XX–XX%). The positive and negative likelihood ratios were
X.X and X.XX, respectively.

Model calibration was good, as assessed by the Hosmer-Lemeshow test
(χ²(8) = [value], p = [value]). Decision curve analysis demonstrated net clinical
benefit of [Model] over the treat-all and treat-none strategies across threshold
probabilities of [X%–Y%]."

Methods statement:
"ROC curves were constructed and areas under the curve (AUC) compared using
the DeLong method (roccomp). Model calibration was assessed visually with
calibration plots and formally with the Hosmer-Lemeshow goodness-of-fit test.
Decision curve analysis was performed using the dca command. Statistical
analyses were performed using STATA version 18 (StataCorp, College Station,
TX, USA)."
```
