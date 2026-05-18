# Deep Manuscript Quality Review — 12 Criteria Reference Guide
# คู่มือรายละเอียดเชิงลึก สำหรับ Mode 6: Deep Manuscript Quality Review

---

## วิธีใช้ไฟล์นี้

ไฟล์นี้ให้รายละเอียดเชิงลึกสำหรับแต่ละ criteria ใน Mode 6
ใช้ควบคู่กับ SKILL.md เพื่อ assign ระดับ 🟢 PASS / 🟡 REVISE / 🔴 MAJOR

**เกณฑ์การ assign ระดับ:**
- 🟢 **PASS** — ผ่านเกณฑ์ทุก checkbox ที่ critical (หรือ minor gap ที่ไม่กระทบ submission)
- 🟡 **REVISE** — ผ่านบางส่วน แต่ต้องปรับปรุง 1–2 จุดก่อน submission; likely ทำให้ reviewers comment
- 🔴 **MAJOR** — ล้มเหลวใน ≥ 1 checkbox ที่ fundamental; อาจทำให้ desk-reject หรือ reject after review

---

## C1 — Research Gap Clarity {#C1}

**เป้าหมาย:** Reader เข้าใจทันทีว่าทำไมงานนี้จึงต้องทำ และ gap คืออะไร

### Detailed Checklist

```
CRITICAL (ขาดอันใดอันหนึ่ง → 🔴 MAJOR):
□ Gap ถูกระบุด้วยหลักฐาน ไม่ใช่แค่ opinion
  ✅ "Only 3 studies have examined X in Y population [refs], and none assessed Z outcome"
  ❌ "Little is known about X" (ไม่มีหลักฐาน)

□ Gap เชื่อมโยงกับ research question ในประโยคสุดท้ายของ Introduction
  ✅ "Therefore, we aimed to [research question], which directly addresses [gap]"
  ❌ Introduction จบแล้ว research question อยู่ห่างจาก gap statement

IMPORTANT (ขาดอันใดอันหนึ่ง → 🟡 REVISE):
□ Gap มีขนาดพอที่ justify ทรัพยากรที่ใช้ในการทำ study
□ Background review ครอบคลุมงานที่เกี่ยวข้องมากที่สุด ≥ 5 ปีล่าสุด
□ ระบุว่างานนี้จะ fill gap ด้วยวิธีใด (study design, population, outcome)
```

### Common Reviewer Comments ที่เกิดจาก C1 Failure
- "The introduction lacks a clear justification for this study"
- "The gap statement is vague — what specific aspect has not been studied?"
- "The authors claim the gap exists but do not cite evidence to support this"

### ตัวอย่างการแก้ไข
```
ก่อน (❌): "Despite growing interest in X, little is known about its effects."
หลัง (✅): "Although 12 RCTs have assessed X in Western populations [refs 1–5],
            none have included Asian populations with [comorbidity],
            who comprise 40% of the global disease burden [ref 6].
            This gap limits the generalizability of current guidelines to this population."
```

---

## C2 — Novelty & Contribution {#C2}

**เป้าหมาย:** งานนี้เพิ่มองค์ความรู้จริง ไม่ใช่แค่ repeat งานเดิมในบริบทอื่น

### Detailed Checklist

```
CRITICAL:
□ Novelty ระบุอย่างเป็นรูปธรรม ≥ 1 ใน 4 ประเภท:
  - Population novelty: กลุ่มประชากรที่ไม่เคยศึกษามาก่อน (มี rationale)
  - Outcome novelty: endpoint ที่สำคัญแต่ไม่เคยวัด
  - Methodological novelty: approach ใหม่ที่แก้ปัญหา limitation ของงานเดิม
  - Contextual novelty: setting/time period ที่เปลี่ยน clinical landscape

□ เปรียบเทียบกับงานที่ใกล้เคียงที่สุด ≥ 2 งาน พร้อมระบุว่างานนี้ต่างอย่างไร

IMPORTANT:
□ Contribution statement ปรากฏใน Abstract และ/หรือ Introduction
□ ถ้าเป็น replication study — มี pre-specified rationale (different population, updated methods)
□ Clinical/public health implication ของ contribution ระบุไว้
```

### Self-Test Questions
1. "ถ้า reviewer อ่าน abstract — เขาจะตอบได้ทันทีว่างานนี้ต่างจาก Smith et al. 2022 อย่างไร?"
2. "Contribution อยู่ที่ไหนใน manuscript? (ถ้าหาไม่เจอใน 2 นาที — ต้องทำให้ชัดขึ้น)"

### Red Flags
- "This is the largest study to date" — ขนาดใหญ่อย่างเดียวไม่ใช่ contribution ถ้าไม่มี novel question
- "We replicated [Study X] in our setting" — ต้องมี rationale ว่าทำไม replication นี้จึงสำคัญ

---

## C3 — Research Design ↔ Research Question Alignment {#C3}

**เป้าหมาย:** Design ที่เลือกสามารถตอบ research question ได้จริงทั้งในทางวิทยาศาสตร์และปฏิบัติ

### Type of Question → Appropriate Design

| Research Question Type | Optimal Design | Acceptable | Problematic |
|---|---|---|---|
| Does X cause Y? (Etiologic) | RCT | Prospective cohort | Cross-sectional |
| What is the accuracy of test X? | Prospective diagnostic | Retrospective (with limitations) | Case-control (spectrum bias) |
| What predicts outcome Y? | Prospective cohort | Retrospective (acknowledged) | ไม่มี control group |
| Does treatment X improve Y? | RCT | Quasi-experimental | Before-after (no control) |
| How common is X? | Cross-sectional population survey | Registry data | Convenience sample (acknowledge) |

### Detailed Checklist

```
CRITICAL:
□ Design ตอบ research question ตาม evidence hierarchy สำหรับ question type นั้น
□ Outcome ที่วัดตอบ question โดยตรง ไม่ใช่ surrogate ที่ห่างไกล
  ✅ RQ: "Does X reduce mortality?" → Outcome: all-cause mortality at 1 year
  ❌ RQ: "Does X reduce mortality?" → Outcome: lab value ที่ surrogate for mortality

IMPORTANT:
□ Follow-up duration เพียงพอสำหรับ outcome ที่สนใจ
□ Comparison group ตอบ counterfactual question ("compared to what?")
□ ถ้า design มีข้อจำกัดในการตอบ question — มี sensitivity analysis หรือ acknowledge ใน limitations
```

### Common Misalignment Patterns
- Cross-sectional design แต่ discuss "effects" หรือ "impact" (เหมาะกับ associations เท่านั้น)
- Retrospective cohort แต่ claim "prospective evidence" ในชื่อ study
- Single-arm study แต่ Conclusions compare กับ "historical controls" โดยไม่ acknowledge limitations

---

## C4 — Argument Flow {#C4}

**เป้าหมาย:** Reader ไม่ต้องหยุดกลับไปอ่านซ้ำ — narrative ไหลสม่ำเสมอตลอดบทความ

### Flow Map ที่ดี
```
Introduction:
  P1 (Why important?) → P2 (What's known? What's missing?) → P3 (Therefore, we did X)
         ↓
Methods: [วิธีที่ตอบ RQ ที่ระบุในท้าย Introduction]
         ↓
Results: [ผลจาก Methods นั้น — primary → secondary → exploratory]
         ↓
Discussion:
  P1 (Main answer to RQ) → P2 (Compare with prior work) → P3 (Mechanism)
  P4 (Limitations) → P5 (Conclusion + implication)
```

### Detailed Checklist

```
CRITICAL:
□ ไม่มี "orphan findings" — ไม่มีผลใน Results ที่ไม่ถูก discuss ใน Discussion
□ Discussion ไม่แนะนำ data ใหม่ที่ไม่ได้อยู่ใน Results
□ Conclusion ไม่เกิน scope ของ findings

IMPORTANT:
□ แต่ละ paragraph มี clear topic sentence
□ Transition ระหว่าง paragraph ใช้ logical connectors ("Furthermore", "In contrast", "This suggests")
□ Methods section ครอบคลุม outcome ทุกตัวที่รายงานใน Results
```

### Quick Test: Topic Sentence Audit
```
ขั้นตอน:
1. ขีดเส้นใต้ topic sentence แรกของทุก paragraph
2. อ่านติดกันทั้งหมด
3. ถ้า narrative ยังสมเหตุสมผล → Argument Flow ดี
4. ถ้ามีการกระโดด topic → ต้องแก้ transition
```

---

## C5 — No Overclaiming in Discussion {#C5}

**เป้าหมาย:** Conclusions ถูก calibrate กับ strength of evidence จริง

### Language Calibration Guide

| Study Type | Appropriate Language | Overclaiming Language |
|---|---|---|
| Cross-sectional | "was associated with", "may be related to" | "caused", "led to", "demonstrates that X causes Y" |
| Retrospective cohort | "was associated with", "predicted", "we observed" | "proves causality", "definitively shows" |
| Prospective cohort | "was independently associated with", "predicted" | "X causes Y", "should change practice" |
| Single RCT | "significantly improved", "reduced", "is effective in" | "X is the standard of care", "all patients should receive" |
| Meta-analysis | "pooled evidence suggests", "there is [quality] evidence" | "conclusively proves", "no further research needed" |

### Detailed Checklist

```
CRITICAL:
□ ไม่ใช้ causal language กับ observational design
□ ไม่ claim "first study ever" โดยไม่ทำ systematic search
□ Conclusions ตรงกับ PICO ของ study (ไม่ extrapolate ไป population อื่น)

IMPORTANT:
□ Abstract conclusions ≤ body conclusions (Abstract ต้องไม่ overclaim มากกว่า body)
□ Clinical recommendations calibrate กับ evidence level
  (pilot study → "warrants further investigation" ไม่ใช่ "should be implemented")
□ Statistical significance ≠ clinical significance — ระบุ clinical meaningfulness
```

### Red Flag Phrases List
```
🔴 ต้องแก้ทันที:
- "proves that X causes Y"
- "definitively demonstrates"
- "this study changes clinical practice"
- "all patients with X should receive Y"
- "X is the standard of care based on our findings"

🟡 ต้องพิจารณา:
- "our results suggest that clinicians should..."
- "strongly suggests causality"
- "unequivocally shows"
- "clearly demonstrates"
```

---

## C6 — All Claims Referenced {#C6}

**เป้าหมาย:** ทุก factual statement มีหลักฐานอ้างอิง — ไม่มี "floating claims"

### Claim Categories ที่ต้องมี Reference

```
MUST CITE (ขาด → 🔴 MAJOR):
□ Epidemiological figures: incidence, prevalence, mortality rates
□ Statements about what "prior studies show/found"
□ Mechanistic claims: "X acts through Y pathway"
□ Guidelines / recommendations ที่อ้างถึง
□ Diagnostic criteria ที่ใช้

SHOULD CITE (ขาด → 🟡 REVISE):
□ Disease burden / economic impact estimates
□ Pathophysiology summaries
□ Standard of care descriptions
□ Definitions ที่ไม่เป็น universal
```

### Detailed Checklist

```
CRITICAL:
□ ไม่มี "it is well known that X" หรือ "studies have shown X" โดยไม่อ้างอิง
□ Abstract claims ทุก statement มี backing ใน body text
□ References ที่ใช้ตรงกับ claim — ไม่ใช่แค่ topically related

IMPORTANT:
□ ไม่มี circular citation (cite งานตัวเอง เพื่อ support งานตัวเอง โดยไม่จำเป็น)
□ Recent references (≤ 5 ปี) สำหรับ evolving fields
□ Primary references แทน secondary/review เมื่อทำได้
```

### Citation Audit Process
```
1. Highlight ทุก factual statement ใน manuscript (สีเหลือง)
2. ตรวจว่าทุก highlighted statement มี [ref] ต่อท้าย
3. สำหรับ statement ที่ไม่มี ref — ต้อง add ref หรือ remove statement
4. ตรวจ ref ที่ cite — เปิดดู abstract จริงว่า support claim นั้นหรือไม่
```

---

## C7 — Structure ↔ Research Question Alignment {#C7}

**เป้าหมาย:** ทุก section ของบทความทำงานร่วมกันเพื่อตอบ research question เดียวกัน

### Structure Alignment Map

```
Research Question (RQ): "Does X improve Y in Z population?"
                              ↓
Methods: "We measured Y in Z population treated with X vs. control"
                              ↓
Results: "Y was significantly better in X group (p=0.02)"
                              ↓
Discussion P1: "Our study shows that X improves Y in Z"
                              ↓
Conclusions: "X is effective for improving Y in Z population"
```

### Detailed Checklist

```
CRITICAL:
□ RQ ระบุใน Introduction paragraph สุดท้าย — ชัดเจน, เป็น sentence เดียว
□ Primary outcome ใน Methods ตรงกับ RQ
□ Results รายงาน primary outcome ก่อน secondary
□ Discussion P1 ตอบ RQ โดยตรง

IMPORTANT:
□ ไม่มี "scope creep" — secondary analyses ไม่ dominate Discussion มากกว่า primary finding
□ Conclusions ตอบ "yes/no/maybe" ต่อ RQ อย่างชัดเจน
□ ถ้า RQ ไม่ได้รับคำตอบชัดเจน — ระบุใน limitations
```

### Quick Alignment Test
```
STEP 1: คัดลอก Research Question
STEP 2: คัดลอก Primary Outcome (Methods)
STEP 3: คัดลอก Main Finding (Results)
STEP 4: คัดลอก Discussion P1 first sentence
STEP 5: คัดลอก Conclusion last sentence

→ ทั้ง 5 ต้องอ่านแล้วเหมือน "tell the same story"
→ ถ้ามี mismatch ในขั้นตอนใด → แก้ขั้นตอนนั้น
```

---

## C8 — Editor-Eye Test {#C8}

**เป้าหมาย:** Manuscript ผ่านการกรอง desk-review ของ editor ก่อนถึง reviewers

### Editor's Desk-Rejection Checklist (Common Reasons)

```
🔴 Instant Desk-Reject (ขาดอันใดอันหนึ่ง):
□ Ethics statement / IRB approval ครบถ้วน
□ Author contributions statement มี
□ Conflict of interest / Funding disclosure มี
□ Word count อยู่ใน journal limit
□ Structured abstract ตรง journal format
□ Manuscript format ตรง journal author guidelines

🟡 Likely Desk-Reject หรือ Return for Revision:
□ Title ไม่ระบุ study design (ถ้า journal กำหนด)
□ Abstract ไม่ structured ตาม journal requirement
□ References format ไม่ตรง journal style
□ Tables/Figures เกิน journal limit
□ ไม่ระบุ trial registration (สำหรับ RCT)
```

### Scope Fit Assessment

```
ตรวจสอบ:
□ Journal scope: งานนี้ตรงกับ topics ที่ journal ระบุใน "Aims and Scope" หรือไม่?
□ Audience: readers ของ journal จะ benefit จากงานนี้หรือไม่?
□ Impact: งานมี potential impact ที่สอดคล้องกับ journal's IF และ mission?
□ Study type: journal รับ research type นี้หรือไม่? (e.g., บาง journal ไม่รับ case-control)

ถ้าตอบ "ไม่" มากกว่า 1 ข้อ → consider different journal
```

### First-Page Test
```
Editor อ่าน manuscript 2 นาทีแรก (Title + Abstract + First paragraph):
□ Title ชัดเจน บอก PICO/study design
□ Abstract สรุปงานได้ครบ: background, methods, key result, conclusion
□ Introduction P1 น่าสนใจ บอก importance ได้ทันที
□ ไม่มี obvious grammatical errors หรือ awkward phrasing ใน 2 หน้าแรก
```

---

## C9 — Reviewer Anticipation {#C9}

**เป้าหมาย:** Address objections ที่ reviewers จะถามก่อนที่พวกเขาจะถาม

### Standard Reviewer Objection Checklist

```
Objection 1: "Why this design?"
  → Methods paragraph แรกอธิบาย design choice พร้อม rationale
  Status: ✅ Addressed / ⚠️ Partially / ❌ Missing

Objection 2: "Why this sample size?"
  → Sample size calculation paragraph ระบุ power, alpha, expected effect size, source
  Status: ✅ Addressed / ⚠️ Partially / ❌ Missing

Objection 3: "What about confounders?"
  → Methods: list potential confounders + how addressed (multivariable, matching, restriction)
  → Limitations: acknowledge residual confounders ที่ยังอาจมี
  Status: ✅ Addressed / ⚠️ Partially / ❌ Missing

Objection 4: "Is the outcome clinically meaningful?"
  → Discussion: อธิบาย clinical significance ไม่ใช่แค่ statistical significance
  → ระบุ minimal clinically important difference (MCID) ถ้ามี
  Status: ✅ Addressed / ⚠️ Partially / ❌ Missing

Objection 5: "Are results generalizable?"
  → Discussion: อธิบาย external validity + limitations to generalizability
  Status: ✅ Addressed / ⚠️ Partially / ❌ Missing

Objection 6: "Why no [standard subgroup/sensitivity analysis]?"
  → ถ้าไม่ได้ทำ analysis ที่คาดหวัง — อธิบายเหตุผล (sample size, data availability)
  Status: ✅ Addressed / ⚠️ Partially / ❌ Missing

Objection 7: "Missing data — how handled?"
  → Methods: ระบุ missing data pattern + handling strategy (complete case, imputation)
  Status: ✅ Addressed / ⚠️ Partially / ❌ Missing
```

### Field-Specific Objections

```
สำหรับ RCT:
□ CONSORT flow diagram มีหรือไม่?
□ Allocation concealment อธิบายไว้หรือไม่?
□ Blinding (ถ้ามี) อธิบายวิธีการ และ verified อย่างไร?
□ Primary outcome pre-specified ใน trial registration หรือไม่?

สำหรับ Diagnostic Accuracy:
□ Index test และ reference standard independent กันหรือไม่?
□ Spectrum ของ disease severity ครอบคลุมหรือไม่?
□ STARD checklist ครบหรือไม่?

สำหรับ Observational:
□ Selection bias: ผู้ที่ excluded ต่างจากผู้ที่ included อย่างไร?
□ Information bias: measurement ของ exposure/outcome มีความเที่ยงตรงแค่ไหน?
```

---

## C10 — Limitations Acknowledged {#C10}

**เป้าหมาย:** Limitations section แสดง intellectual honesty และช่วย readers ตีความผลอย่างถูกต้อง

### Limitations Quality Assessment

```
CRITICAL (ขาด → 🔴 MAJOR):
□ Limitations section มีอยู่จริงใน Discussion (ไม่ใช่แค่ 1 ประโยค)
□ Limitations เฉพาะเจาะจงกับ study นี้ ไม่ใช่ generic ("all retrospective studies have limitations")
□ แต่ละ limitation อธิบาย potential impact ต่อ findings

IMPORTANT (ขาด → 🟡 REVISE):
□ ลำดับ limitations จาก most significant → least
□ ไม่ dilute limitations ด้วยการพูดถึง strengths ก่อนทุกข้อ
□ Limitations ไม่ขัดแย้งกับ strength of conclusions
```

### Common Limitations ที่มักถูก Underreport

```
1. Selection bias
   ✅ "Patients were recruited from a tertiary center, which may select for more severe disease,
      potentially overestimating the complication rate compared to community settings"
   ❌ "This was a single-center study" (ไม่อธิบาย mechanism ของ bias)

2. Information bias / Measurement error
   ✅ "Exposure was ascertained from medical records, which may underestimate
      actual exposure due to incomplete documentation"

3. Residual confounding
   ✅ "Despite multivariable adjustment, unmeasured confounders such as
      [specific variable] may still influence our results"

4. Loss to follow-up / Missing data
   ✅ "17% of participants were lost to follow-up; if these patients had worse outcomes,
      our effect estimates may be biased toward the null"

5. Temporal limitation
   ✅ "Data were collected between 2015–2019; subsequent changes in
      clinical practice may affect generalizability to current practice"
```

### Limitations ≠ Fatal Flaw Test
```
ถาม: "Limitation นี้ทำให้ results ไม่น่าเชื่อถือทั้งหมดหรือเปล่า?"
→ ถ้าใช่ → ต้องแก้ design หรือ tone down conclusions อย่างมาก
→ ถ้าไม่ใช่ → ระบุ limitations แล้วอธิบาย why results are still valid despite limitations
```

---

## C11 — Methodology Justified {#C11}

**เป้าหมาย:** Reader และ reviewer เข้าใจว่าทำไมจึงทำแต่ละขั้นตอน และสามารถ replicate ได้

### Methodological Justification Checklist

```
CRITICAL:
□ Statistical method เหมาะสมกับ data distribution
  (Normal → t-test, Skewed → Mann-Whitney, Binary → logistic regression, Time-to-event → Cox)
□ Cutpoints/thresholds มี pre-specified definition + reference (ไม่ใช่ post-hoc)
□ Sample size calculation: power, alpha, expected effect, basis for estimate, source

IMPORTANT:
□ Primary vs. secondary outcomes designated ก่อนเก็บข้อมูล หรือ acknowledge ถ้าไม่ได้
□ Sensitivity analyses ทำเมื่อ key assumptions อาจส่งผล (missing data mechanism, outliers)
□ Subgroup analyses pre-specified หรือ labeled "exploratory"
□ Multiple testing: Bonferroni / FDR correction ถ้า ≥ 5 comparisons ที่ primary
```

### Replication Test
```
สร้าง scenario: "นักวิจัยอีกทีมอ่าน Methods ของเรา และพยายาม replicate study"

ถาม:
□ เขาจะรู้ว่า include/exclude ใครได้อย่างชัดเจนหรือไม่?
□ เขาจะรู้วิธีวัด outcome อย่างไรหรือไม่?
□ เขาจะรู้ว่าใช้ statistical test อะไร และ software อะไรหรือไม่?
□ เขาจะได้ผลที่ใกล้เคียงกันหรือไม่?

→ ถ้า "ไม่รู้" มากกว่า 1 ข้อ → Methods ต้องระบุรายละเอียดเพิ่ม
```

### Statistical Method Justification Template
```
สำหรับแต่ละ statistical test ที่ใช้:
"[Test name] was used to compare [variable] between groups because
[data were normally distributed / the outcome was binary / time to event was the endpoint].
[Software, version] was used for all analyses."
```

---

## C12 — Generalizability Claim Appropriate {#C12}

**เป้าหมาย:** Conclusions ไม่ overgeneralize เกินกว่าที่ evidence รองรับ

### Generalizability Evidence Pyramid

```
Strength of Generalizability Claim ที่ appropriate:

Single-center, convenience sample
    → "may not be generalizable beyond similar settings"
    → "warrants confirmation in broader populations"

Multi-center, selected population
    → "findings may apply to patients with similar characteristics"
    → "results should be confirmed in [different setting]"

Population-based, representative sample
    → "findings likely generalizable to [defined population]"
    → "results may inform practice in similar health systems"

Multi-country RCT / Meta-analysis
    → "findings are applicable to [defined population]"
    → "evidence supports [intervention] in [defined population]"
```

### Detailed Checklist

```
CRITICAL:
□ Conclusions ไม่ claim applicability เกินกว่า study population
  ✅ "Our findings may apply to adult patients with [condition] in tertiary hospitals"
  ❌ "Our findings apply to all patients with [condition]"

□ ถ้า single-center — ระบุ characteristics ของ center ที่อาจแตกต่าง
  (academic vs. community, volume, resources, patient demographics)

IMPORTANT:
□ Temporal generalizability — ถ้า data เก่า (>5 ปี) acknowledge practice changes
□ Geographic generalizability — healthcare system, ethnicity, diet, genetic factors
□ Patient generalizability — age range, comorbidities, disease severity ในงาน
```

### Context-Specific Considerations

```
Single-center study (Thailand):
→ ระบุว่าเป็น tertiary / secondary / primary care
→ Volume (จำนวน cases ต่อปี) ถ้าเกี่ยวข้อง
→ Acknowledge ว่า results อาจต่างใน lower-resource settings

Asian population study:
→ ระบุ ethnicity อย่างเฉพาะเจาะจง (Thai / Chinese / Japanese ไม่ใช่แค่ "Asian")
→ Acknowledge genetic/dietary/environmental differences ถ้าเกี่ยวข้อง
→ ระบุว่า results อาจแตกต่างจาก Western studies (ถ้ามี known differences)
```

---

## Overall Deep Review — Decision Matrix

```
╔═══════════════════════════════════════════════════════════════╗
║                    SUBMISSION DECISION                        ║
╠═══════════════════════════════════════════════════════════════╣
║ 🔴 MAJOR ≥ 2 criteria   → Revise substantially, re-review    ║
║ 🔴 MAJOR = 1 criteria   → Address MAJOR, then re-review      ║
║ 🟡 REVISE ≥ 4 criteria  → Revise before submission            ║
║ 🟡 REVISE 1–3 criteria  → Minor revision, then submit         ║
║ 🟢 All PASS             → Ready to submit                    ║
╚═══════════════════════════════════════════════════════════════╝
```
