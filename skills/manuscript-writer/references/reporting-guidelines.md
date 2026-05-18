# Reporting Guidelines for Medical Research
# แนวทางการรายงานงานวิจัยทางการแพทย์

## 1. CONSORT 2010 — Randomized Controlled Trials

### Checklist หลัก (25 items)

**Title & Abstract**
- [ ] CONSORT structured abstract ครบทุก element
- [ ] ระบุ study design ใน title

**Introduction**
- [ ] Scientific background and explanation of rationale
- [ ] Specific objectives or hypotheses

**Methods — Participants**
- [ ] Eligibility criteria
- [ ] Settings and locations

**Methods — Interventions**
- [ ] Interventions for each group (sufficient detail for replication)

**Methods — Outcomes**
- [ ] Pre-specified primary and secondary outcome measures
- [ ] Any changes to trial outcomes after trial commencement

**Methods — Sample Size**
- [ ] How sample size was determined
- [ ] Explanation of any interim analyses and stopping guidelines

**Methods — Randomization**
- [ ] Method used to generate random allocation sequence
- [ ] Type of randomization (block, stratified)
- [ ] Mechanism used to implement allocation concealment

**Methods — Blinding**
- [ ] Who was blinded (participants, caregivers, assessors)
- [ ] How blinding was achieved

**Methods — Statistics**
- [ ] Statistical methods used for primary and secondary outcomes
- [ ] Methods for additional analyses (subgroup, adjusted)

**Results — Participant Flow (CONSORT diagram)**
- [ ] Number in each group at each stage
- [ ] Losses and exclusions

**Results — Baseline Data**
- [ ] Table 1 with demographics and clinical characteristics

**Results — Outcomes**
- [ ] Results for each primary and secondary outcome
- [ ] For binary outcomes: absolute and relative effect size

**Discussion**
- [ ] Trial limitations, sources of potential bias
- [ ] Generalisability (external validity)
- [ ] Interpretation consistent with results, balancing benefits and harms

### CONSORT Abstract Template
```
Background: [1-2 sentences on why study needed]
Objective: To [compare/evaluate/determine] whether [intervention] [reduces/improves]
           [outcome] compared with [control] in [population].
Methods: Design: Randomized controlled trial. Setting: [setting]. Participants:
         [n] adults with [condition]. Interventions: [brief description].
         Main outcome measures: Primary: [outcome]. Secondary: [outcomes].
Results: Of [n] randomized, [n] completed the study. [Primary outcome result
         with statistics]. Secondary outcomes: [brief]. Adverse events: [brief].
Conclusions: [Main conclusion in 1 sentence]. [Implication or recommendation].
Registration: [ClinicalTrials.gov: NCT number]
```

---

## 2. STROBE 2007 — Observational Studies

### Key Items

**Title and Abstract**
- [ ] Indicate the study's design with a commonly used term

**Introduction**
- [ ] Explain the scientific background and rationale
- [ ] State specific objectives, including any pre-specified hypotheses

**Methods — Study Design**
- [ ] Present key elements of study design early in the paper

**Methods — Setting**
- [ ] Describe the setting, locations, and relevant dates

**Methods — Participants**
- [ ] Cohort: eligibility criteria, follow-up period
- [ ] Case-control: criteria for cases/controls, source of controls
- [ ] Cross-sectional: eligibility criteria, data source

**Methods — Variables**
- [ ] Clearly define outcome, exposure, predictors, confounders, effect modifiers

**Methods — Bias**
- [ ] Describe any efforts to address potential sources of bias

**Methods — Sample Size**
- [ ] Explain how the study size was arrived at

**Results**
- [ ] Report numbers of individuals at each stage (flowchart)
- [ ] Give characteristics of study participants (Table 1)
- [ ] Report unadjusted AND adjusted estimates

**Discussion**
- [ ] Summarise key results
- [ ] Discuss limitations (selection bias, information bias, confounding)
- [ ] Give a cautious overall interpretation considering objectives, limitations,
      multiplicity of analyses, results from similar studies, and other evidence

---

## 3. STARD 2015 — Diagnostic Accuracy Studies

### 30-Item Checklist

**Introduction**
- [ ] Scientific and clinical background
- [ ] Study objectives and hypotheses

**Methods — Study Design**
- [ ] Prospective, retrospective, or designed as part of diagnostic study
- [ ] Data collection: before or after index test results

**Methods — Participants**
- [ ] Eligibility criteria, recruitment, enrolment setting
- [ ] Sample size

**Methods — Test Methods**
- [ ] Index test: protocol, reader qualifications, blinding to reference standard
- [ ] Reference standard: rationale for choice, blinding to index test

**Methods — Analysis**
- [ ] Methods for estimating diagnostic accuracy
- [ ] How indeterminate results, missing data, outliers handled
- [ ] Intended cut-off values
- [ ] Software and version

**Results — Participants**
- [ ] Flow of participants (STARD flow diagram)
- [ ] Baseline demographics

**Results — Test Results**
- [ ] Cross-tabulation (2×2 table) for each cutoff value
- [ ] Estimates of diagnostic accuracy with 95% CI

**Discussion**
- [ ] Study limitations (spectrum bias, indeterminate results)
- [ ] Clinical applicability

---

## 4. TRIPOD 2015 — Prediction Models

**Target:** Development or validation of clinical prediction models

### Key Items

**Methods**
- [ ] Study design and source of data
- [ ] Eligibility criteria
- [ ] Outcome and predictors
- [ ] Sample size justification
- [ ] Missing data handling
- [ ] Model development: variable selection, shrinkage
- [ ] Performance measures: discrimination (AUC) and calibration

**Results**
- [ ] Participants flowchart
- [ ] Baseline characteristics
- [ ] Model performance: AUC with 95% CI, calibration plot, Hosmer-Lemeshow
- [ ] Final model presentation (formula, nomogram, score)

**Discussion**
- [ ] Limitations (overfitting, need for external validation)

---

## 5. Journal-Specific Word Limits

| Journal | Type | Abstract | Main text | References |
|---------|------|----------|-----------|------------|
| JAMA | Original | 350 | 3,000 | 40 |
| NEJM | Original | 150 structured | 2,700 | 70 |
| Lancet | Original | 150 | 4,000 | 60 |
| BMJ | Research | 150 | 3,000 | 50 |
| Annals | Original | 250 structured | 3,000 | 40 |
| PLOS ONE | Research | 300 | No limit | No limit |
| Scientific Reports | Research | 200 | No limit | No limit |

---

## 6. Statistical Reporting Standards (APA/CONSORT/STROBE)

### P-values
```
- รายงาน exact p-values (ไม่เขียน NS)
- p < 0.001 สำหรับค่าที่น้อยมาก
- ไม่เขียน p = 0.000
- ตัวอย่าง: p = 0.023, p < 0.001, p = 0.642
```

### Effect Sizes
```
- ต้องรายงาน effect size ทุกครั้ง ไม่ใช่แค่ p-value
- Binary: OR, RR, HR พร้อม 95% CI
- Continuous: mean difference, Cohen's d
- Survival: HR พร้อม 95% CI
- Diagnostic: AUC พร้อม 95% CI
```

### Confidence Intervals
```
- ใช้ 95% CI เสมอ
- รูปแบบ: (95% CI lower–upper) หรือ [lower, upper]
- ตัวอย่าง: OR 1.45 (95% CI 1.12–1.88)
```

### Decimal Places
```
- Mean, SD, median, IQR: 1 decimal place
- Percentages: 1 decimal place
- OR, RR, HR: 2 decimal places
- P-values: 3 decimal places
- AUC: 3 decimal places
```
