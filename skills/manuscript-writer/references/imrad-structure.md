# IMRAD Structure Guide
# คู่มือโครงสร้าง IMRAD สำหรับงานวิจัยทางการแพทย์

## Overview: IMRAD Structure

```
Title (ชื่อเรื่อง)
├── Abstract (บทคัดย่อ)
├── Introduction (บทนำ) — WHY
├── Methods (วิธีการ) — HOW
├── Results (ผลการศึกษา) — WHAT HAPPENED
└── Discussion (อภิปราย) — WHAT IT MEANS
    └── Conclusion
```

---

## 1. Title

### ประเภทชื่อเรื่อง
```
1. Descriptive (บอกสิ่งที่ศึกษา):
   "Efficacy of [Drug X] for [Condition Y] in [Population Z]: A Randomized Controlled Trial"

2. Question (ตั้งเป็นคำถาม):
   "Does [Drug X] Reduce [Outcome Y] in Patients with [Condition Z]?"

3. Results-forward (ขึ้นต้นด้วยผล):
   "[Drug X] Significantly Reduces [Outcome Y] in [Population Z]: A Phase III Trial"

4. Colon format:
   "[Main finding]: [Study description]"
   "Improved Survival with [Drug X]: A Randomized Trial in [Condition Y]"
```

### หลักการ
- ≤ 15 words (บางวารสาร ≤ 12)
- ระบุ study design สำหรับ RCT และ systematic review
- ไม่ใช้ abbreviations ใน title
- ไม่ใช้ "A Study of..." หรือ "Investigation into..."

---

## 2. Abstract

### Structured Abstract (RCT/Observational)
```
BACKGROUND
[1-3 sentences: บอก clinical problem, prevalence/burden, gap in knowledge]

METHODS
Design: [Prospective/Retrospective] [study type] conducted from [dates].
Setting: [single/multi-center], [hospital/community].
Participants: [n] [patients/participants] with [condition/criteria]. Exclusions included [brief].
Intervention/Exposure: [brief description].
Main Outcome Measures: Primary: [outcome and measurement]. Secondary: [outcomes].

RESULTS
Of [n total approached], [n randomized/enrolled]. [n] completed follow-up.
[Primary outcome result: mean/%, effect size, 95% CI, p-value].
[Secondary outcomes: 1-2 key results].
[Safety/adverse events if applicable].

CONCLUSIONS
[Main conclusion in 1 sentence]. [Clinical implication in 1 sentence].
[Trial Registration: if RCT]
```

### Unstructured Abstract (some journals)
```
[Background 2 sentences] [Objective 1 sentence] [Methods 2-3 sentences]
[Results 2-3 sentences with key numbers] [Conclusions 1-2 sentences]
```

---

## 3. Introduction

### 3-Paragraph Template

**Paragraph 1 — Background (Problem)**
```
[Condition X] affects approximately [N] million people worldwide, with an
annual incidence of [X per 100,000]. [1-2 sentences on morbidity/mortality/burden].
Current standard of care includes [treatments], which achieves [outcomes] in [X%]
of patients; however, [problem/unmet need].
```

**Paragraph 2 — Gap (What we don't know)**
```
Previous studies have investigated [related area], demonstrating [findings]. [Ref]
However, [specific limitation 1], [specific limitation 2], and [limitation 3] have
prevented translation to [population/setting]. In particular, [the most important gap].
```

**Paragraph 3 — Rationale + Objective**
```
Given [reason], we hypothesized that [hypothesis]. Therefore, we conducted a
[study design] to [primary objective]. Secondary objectives included [secondary aims].
```

---

## 4. Methods

### Universal Subsections
```
4.1 Study Design and Setting
4.2 Participants
    - Inclusion criteria
    - Exclusion criteria
    - Recruitment and enrollment
4.3 [Intervention / Exposure / Index Test]
4.4 Outcomes
    - Primary outcome
    - Secondary outcomes
    - How measured, by whom, when
4.5 Sample Size
4.6 Statistical Analysis
```

### Statistical Analysis Section Templates

**สำหรับ RCT:**
```
Statistical analysis was performed on an intention-to-treat basis, including all
randomized participants. Continuous variables are presented as mean ± standard
deviation (SD) or median (interquartile range, IQR) depending on the distribution
assessed by Shapiro-Wilk test. Between-group comparisons of continuous variables
used Student's independent-samples t-test or Mann-Whitney U test, as appropriate.
Categorical variables are expressed as frequencies and percentages and compared
using chi-square test or Fisher's exact test.

The primary outcome was analyzed using [test/model]. Secondary outcomes were
analyzed using [methods]. Multivariable [logistic/linear] regression was used to
adjust for [prespecified covariates]. Results are presented as [OR/RR/HR/mean difference]
with 95% confidence intervals (CIs). Missing data were handled by [method].
Subgroup analyses were pre-specified and tested using interaction terms.
A two-sided α = 0.05 was used as the significance threshold for all tests.
All analyses were conducted using Python (version [X]) with scipy [version],
statsmodels [version], and lifelines [version].
```

**สำหรับ Survival Analysis:**
```
Time-to-event outcomes were analyzed using the Kaplan-Meier method, with
between-group differences assessed by the log-rank test. Cox proportional
hazards regression was used to estimate hazard ratios (HRs) with 95% CIs,
adjusted for [covariates]. The proportional hazards assumption was verified
using Schoenfeld residuals and log-log survival plots. Restricted mean survival
time (RMST) at [X] months was calculated as a supplementary measure.
```

**สำหรับ Diagnostic Accuracy:**
```
Diagnostic performance was assessed by constructing receiver operating
characteristic (ROC) curves and calculating the area under the curve (AUC).
The optimal cutoff value was determined using Youden's index. Sensitivity,
specificity, positive predictive value (PPV), negative predictive value (NPV),
and likelihood ratios were calculated at the optimal cutoff with 95% CIs
using the Wilson score method. Differences between AUCs were tested using
the DeLong method. Calibration was assessed using the Hosmer-Lemeshow test
(well-calibrated if p > 0.05). Decision curve analysis evaluated clinical utility.
```

---

## 5. Results

### Flow Diagram (first paragraph)
```
Between [date] and [date], [N] patients were screened, of whom [N] were enrolled.
[N] were excluded due to [reasons]. [Figure 1 — CONSORT/STROBE flowchart].
Of those enrolled, [N] were assigned to [Group A] and [N] to [Group B].
[N] patients completed the study period; [N] were lost to follow-up ([reasons]).
```

### Table 1 — Baseline Characteristics
```
Table 1. Baseline Demographic and Clinical Characteristics

The two groups were well balanced at baseline (Table 1). The mean age was
[X.X ± X.X] vs [X.X ± X.X] years (p = [X]), and [X]% vs [X]% were male.
[Any notable differences].
```

### Primary Outcome
```
For the primary outcome of [outcome], the [Group A] achieved [X.X%/X.X months]
compared with [X.X%/X.X months] in [Group B] ([test statistic], p = [X.XXX]).
This corresponded to a [relative risk reduction/odds ratio/hazard ratio] of
[X.XX] (95% CI [X.XX]–[X.XX]) favoring [Group A/B] [Figure 2].
```

### Secondary Outcomes (table format)
```
Secondary outcomes are presented in Table 2. [Key secondary finding 1].
[Key secondary finding 2]. No significant differences were observed in [outcome 3]
(OR [X.XX], 95% CI [X.XX]–[X.XX], p = [X.XXX]).
```

---

## 6. Discussion

### 5-Paragraph Template

**Paragraph 1 — Main Findings (Summary)**
```
In this [study design] of [N] patients with [condition], we found that [main finding 1],
[main finding 2], and [main finding 3]. These results [support/refute/extend]
the hypothesis that [hypothesis].
```

**Paragraph 2 — Contextualize (Compare with literature)**
```
Our findings are consistent with [prior study] which demonstrated [finding in similar population].
[Ref] However, our study extends these observations by [how it's different/better].
In contrast, [conflicting study] reported [different finding], which may be explained
by [differences in population/methodology/follow-up].
```

**Paragraph 3 — Mechanism / Biological Plausibility**
```
The observed [effect] may be explained by [mechanism]. Preclinical studies have shown
[relevant biology]. [Ref] Furthermore, [additional supporting evidence].
```

**Paragraph 4 — Limitations (Honest)**
```
Several limitations of this study should be acknowledged. First, [limitation 1 and impact].
Second, [limitation 2 and impact]. Third, [limitation 3]. Additionally, [limitation 4].
These limitations may affect the generalizability of our findings to [populations/settings].
```

**Paragraph 5 — Conclusions**
```
In conclusion, [restate main finding in different words]. These findings suggest that
[implication for clinical practice/future research]. [Final sentence on broader significance
or call to action].
```

---

## 7. References Format

### Vancouver (Numeric) — most medical journals
```
1. Smith AB, Jones CD. Title of article. J Med. 2023;45(3):123-30. doi:10.xxx
```

### Key rules:
- List all authors ถ้า ≤ 6; ถ้า > 6 ให้ใส่ 6 คน แล้วตามด้วย "et al."
- ใช้ PubMed MEDLINE abbreviations สำหรับชื่อวารสาร
- ระบุ DOI ทุกครั้งที่มี
