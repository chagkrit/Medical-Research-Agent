---
name: peer-review
description: >
  ใช้ skill นี้เมื่อผู้ใช้ต้องการ "peer review manuscript", "deep review", "reviewer mode",
  "ตรวจ manuscript เชิงลึก", "Q1 reviewer simulation", "anticipate reviewer comments",
  "revision plan", "pre-submission review", "internal review", "blinded review",
  "research gap clarity", "novelty check", "argument flow check", "overclaim check",
  "limitations completeness", "methodology justification", "generalizability check",
  "editor eye test", "12-criteria review", "checklist review", "reviewer 2 simulation"
metadata:
  version: "1.0.0"
  author: "Chagkrit"
  target_tier: "Q1 high-impact-factor"
---

# Peer Review — Reviewer-Grade Deep Review
# ระบบ Peer Review แบบ Senior Reviewer ของ Q1/Q2 Journal

## บทบาท
ทำหน้าที่เป็น **Senior Peer Reviewer** ของ Q1 journal — ตรวจ manuscript ด้วย:
1. **12-criteria deep review** (conceptual integrity)
2. **Reviewer Anticipation Matrix** (objections + status)
3. **Revision Planner** (priority + effort + impact)
4. **Reporting-guideline gap analysis** (CONSORT/STROBE/STARD/TRIPOD/PRISMA)
5. **Statistical method scrutiny**

Output: simulation ที่ใกล้เคียงกับสิ่งที่ reviewer จะเขียนเข้ามาจริง

---

## Q1 Reviewer Mental Model

Reviewer ของ Q1/Q2 journal จะถามคำถามต่อไปนี้:
1. **Is the question important enough for this journal?** (scope/audience)
2. **Is the gap real and well-supported?** (literature)
3. **Does the design actually answer the question?** (methodology)
4. **Is the sample adequate?** (power, generalizability)
5. **Are the statistics correct and pre-specified?** (rigor)
6. **Do the results support the claims?** (overclaim check)
7. **Is the discussion balanced (incl. disagreements)?** (literature breadth)
8. **Are limitations honest and consequential?** (intellectual honesty)
9. **Could a reader replicate this?** (transparency)
10. **Will clinicians/researchers change practice/research because of this?** (impact)

---

## Smart Router

| Trigger | Mode |
|---|---|
| "peer review" / "deep review" / "ตรวจ manuscript" | **Mode 1 — 12-Criteria Deep Review** |
| "reviewer anticipation" / "objections" | **Mode 2 — Reviewer Anticipation Matrix** |
| "revision plan" / "what to fix first" | **Mode 3 — Revision Planner** |
| "reporting guideline gap" | **Mode 4 — Guideline Gap Analysis** |
| "stats review" / "biostatistics critique" | **Mode 5 — Statistical Methods Scrutiny** |
| "blinded review" / "simulate Reviewer 2" | **Mode 6 — Reviewer 2 Simulation** |

> เริ่มต้นทุก peer review ด้วย **Mode 1**; แล้วเสนอ Mode 2–6 ตามผลที่พบ

---

## Mode 1 — 12-Criteria Deep Review

แต่ละ criterion: 🟢 PASS | 🟡 REVISE | 🔴 MAJOR

### C1 — Research Gap Clarity
```
□ Introduction ระบุ gap ด้วยหลักฐาน (ไม่ใช่ "little is known")
□ Gap ใหญ่พอที่จะ justify การทำ study นี้
□ Gap เชื่อมโดยตรงกับ research question
□ อ้างอิงงานที่มีอยู่ครบก่อนระบุว่า "ขาด"
□ ระบุชัดว่า study นี้ fill gap อย่างไร
Test: "If reader asks 'why do this study?', is the answer obvious in Introduction?"
```

### C2 — Novelty & Contribution
```
□ ระบุ novelty อย่างเป็นรูปธรรม (new population/outcome/method/context)
□ เปรียบเทียบกับ ≥2 closest studies — ต่างกันอย่างไร
□ ไม่ใช่แค่ "replicate in different setting" โดยไม่มี rationale
□ Contribution ระบุใน abstract + intro paragraph 3
□ Clinical/methodological/theoretical significance ชัด
Test: "If this study didn't exist, what knowledge would be missing?"
```

### C3 — Design ↔ Question Alignment
```
□ Design เหมาะกับ question type (etiologic/diagnostic/prognostic/therapeutic)
□ Outcome ตอบ question โดยตรง (ไม่ใช่ proxy ห่างไกล)
□ Follow-up / sample size / timing ครบ
□ Comparison group (ถ้ามี) ตอบ question
□ ข้อจำกัดของ design ที่กระทบ question ระบุใน limitations
Test: "Put research question next to design — does design answer it?"
```

### C4 — Argument Flow
```
□ Intro → Methods → Results → Discussion = ลำดับตรรกะเดียวกัน
□ ทุก paragraph มี topic sentence ชัด
□ ไม่มี logical jump (ข้ามขั้นตอน reasoning)
□ Discussion ไม่แนะนำข้อมูลใหม่ที่ไม่ปรากฏใน Results
□ Conclusions ตามมาจาก findings โดยตรง
Test: "Read topic sentences of every paragraph in order — does the narrative make sense?"
```

### C5 — Overclaiming Check
```
□ ไม่ใช้ causal language กับ observational design ("associated" vs "causes")
□ ไม่ extrapolate เกิน study population
□ ไม่ claim "first study" โดยไม่ตรวจ literature ครบ
□ Conclusions ระบุ strength of evidence (pilot ≠ definitive)
□ ไม่ใช้ "prove", "demonstrate causality" เมื่อ evidence ไม่พอ

Red flags: "proves", "demonstrates that X causes Y", "definitively shows",
"all patients with X should receive Y", "this study changes clinical practice"
```

### C6 — All Claims Referenced
```
□ ทุก epidemiological number มี citation
□ ทุก "prior studies show…" มี citation ที่ถูกต้อง
□ ทุก mechanistic claim มี evidence รองรับ
□ ไม่มี "well known…" หรือ "studies have shown…" lone
□ References ตรงกับ claim จริง (ไม่ใช่แค่ topically related)
□ ไม่มี self-citation ที่ไม่จำเป็น
Special: Abstract claims ต้องมี backing ในบอดี + references
```

### C7 — Structure ↔ Research Question
```
□ Research question ระบุชัดใน intro paragraph สุดท้าย
□ Methods วัด outcome ที่ตอบ RQ โดยตรง
□ Results รายงาน primary outcome ก่อน
□ Discussion paragraph แรก summarize คำตอบของ RQ
□ Conclusions กลับมาที่ RQ
Test: "Read RQ then jump to Conclusions — do they match?"
```

### C8 — Editor-Eye Test (desk-reject screening)
```
□ Title ชัด, ระบุ design + population
□ Abstract เข้าใจงานทั้งหมดใน 30 วินาที
□ Scope fit journal ที่ target
□ First intro paragraph น่าสนใจ + ระบุ importance
□ ไม่มี formatting errors, undefined abbreviations
□ Word count + structure ตรง guidelines
□ Ethical statement, author contrib, COI ครบ
Test: "If I'm editor and have 50 manuscripts today — send for review or desk reject?"

Common desk-reject reasons:
- scope mismatch, poor writing, obvious methodological flaw,
  duplicate submission signs, missing ethics statement, plagiarism risk
```

### C9 — Reviewer Anticipation
```
□ "Why this design?" — justified
□ "Why this sample size?" — power calc shown
□ "What about confounders?" — addressed / acknowledged
□ "Is outcome clinically meaningful?" — explained
□ "Are results generalizable?" — external validity discussed
□ "Why no [standard analysis]?" — reasoned

Generate: Anticipated Reviewer Objection List
```

### C10 — Limitations
```
□ Limitations section exists (not just passing mention)
□ Specific to this study (not generic boilerplate)
□ Each limitation describes impact on findings
□ Not diluted by excessive strengths before
□ Doesn't contradict conclusions
□ Suggestions for future research linked to limitations

Special: Missing data / attrition / selection / measurement bias addressed?
```

### C11 — Methodology Justification
```
□ Every important methodological choice has rationale ("we used X because Y")
□ Stats appropriate for data distribution + design
□ Cutpoints/thresholds pre-specified or referenced
□ Sensitivity analyses when assumptions matter
□ Multiplicity addressed (if multiple endpoints)
□ Protocol/SAP pre-registration — results match?
Test: "Can a reviewer replicate every step from Methods?"
```

### C12 — Generalizability
```
□ Conclusions don't claim generalizability beyond study population
□ Single-center — practice variation acknowledged
□ Specific population (elderly/Asian/urban) — limits clear
□ Sample context (country, era, setting) in Methods
□ External validity discussion proportionate
Match claim to internal validity (observational → "may inform" / RCT → stronger)
```

---

## Mode 1 Output — Scorecard

```
╔════════════════════════════════════════════════════════════════╗
║        DEEP MANUSCRIPT QUALITY REVIEW REPORT — Q1 SIM         ║
╚════════════════════════════════════════════════════════════════╝

Manuscript: <title>
Date: <date>
Target journal: <name> | Quartile: Q1 | IF: <n>

──────────────────────────────────────────────────────────────────
SUMMARY SCORECARD
──────────────────────────────────────────────────────────────────
C1  Research Gap Clarity         🟢/🟡/🔴   <one-line verdict>
C2  Novelty & Contribution       🟢/🟡/🔴   <verdict>
C3  Design ↔ Question Alignment  🟢/🟡/🔴   <verdict>
C4  Argument Flow                🟢/🟡/🔴   <verdict>
C5  No Overclaiming              🟢/🟡/🔴   <verdict>
C6  All Claims Referenced        🟢/🟡/🔴   <verdict>
C7  Structure ↔ RQ Alignment     🟢/🟡/🔴   <verdict>
C8  Editor-Eye Test              🟢/🟡/🔴   <verdict>
C9  Reviewer Anticipation        🟢/🟡/🔴   <verdict>
C10 Limitations Acknowledged     🟢/🟡/🔴   <verdict>
C11 Methodology Justified        🟢/🟡/🔴   <verdict>
C12 Generalizability Appropriate 🟢/🟡/🔴   <verdict>

Overall: X/12 PASS | Y REVISE | Z MAJOR
──────────────────────────────────────────────────────────────────
PRIORITY ACTION LIST
──────────────────────────────────────────────────────────────────
🔴 MAJOR (fix before submission):
1. [C#] <issue> → <specific fix with example>

🟡 REVISE (improve odds):
1. [C#] <issue> → <fix>

🟢 STRENGTHS (preserve):
1. [C#] <good thing>
──────────────────────────────────────────────────────────────────
RECOMMENDATION
──────────────────────────────────────────────────────────────────
□ Ready to submit
□ Minor revisions before submission
□ Major revisions — revise and re-review
╚════════════════════════════════════════════════════════════════╝
```

---

## Mode 2 — Reviewer Anticipation Matrix

ทำตารางคำถามที่ reviewer น่าจะถาม:

| # | Anticipated Objection | Location ที่ address แล้ว | Status | Suggested response/fix |
|---|---|---|---|---|
| 1 | Selection bias — how recruited? | Methods §2.1 | 🟡 Partial | Add table of approached vs enrolled vs analysed |
| 2 | Why not stratify by X? | — | 🔴 Missing | Add sensitivity stratified by X |
| 3 | Calibration of model? | — | 🔴 Missing | Add calibration plot + Brier score |
| 4 | Missing data handling? | Methods §2.5 | 🟢 Addressed | OK |
| 5 | Multiplicity? | — | 🔴 Missing | Add multiplicity strategy + pre-spec |

Output: full matrix → push to revision planner

---

## Mode 3 — Revision Planner

Prioritization grid:

| Issue | Impact on acceptance | Effort | Priority |
|---|---|---|---|
| Add calibration plot | HIGH | LOW | 🔴 P1 |
| Address selection bias | HIGH | MED | 🔴 P1 |
| Restructure Discussion ¶2 | MED | LOW | 🟡 P2 |
| Replace 2 references with Q1 | LOW | LOW | 🟡 P2 |
| Re-run sensitivity | HIGH | HIGH | 🔴 P1 (next sprint) |

Output: ordered checklist + estimated hours + dependency graph

---

## Mode 4 — Reporting-Guideline Gap Analysis

ตาม design:
- CONSORT 2010 — 25 items × line-by-line check
- STROBE 2007 — 22 items
- STARD 2015 — 30 items
- TRIPOD 2015 — 22 items
- PRISMA 2020 — 27 items
- CARE / SRQR / ARRIVE — design-specific

```
CONSORT 2010 Compliance
────────────────────────
✅ 1a Title identifies trial as randomised
✅ 1b Structured CONSORT abstract
❌ 5  Intervention sufficient to replicate — MISSING dose schedule
⚠️ 7a Sample size determination — incomplete (no power calculation reference)
❌ 12a Statistical methods — multiplicity not stated
✅ 22 Limitations
...
Compliance: 19/25 (76%) → 6 fixes required before submission
```

---

## Mode 5 — Statistical Methods Scrutiny

Senior biostatistician checklist:
```
□ Pre-registered SAP? Locked before unblinding?
□ Effect estimate + 95% CI + exact p reported?
□ Multiplicity adjustment specified?
□ Assumption checks reported (normality, PH, linearity, calibration)?
□ Sensitivity analyses pre-specified?
□ Handling of missing data described and adequate?
□ Model selection process reproducible?
□ Subgroup analyses pre-specified + interaction p reported?
□ Software + version + seed reported?
□ Non-inferiority margin justified (if NI)?
□ For prediction model: discrimination + calibration + DCA reported?
□ For causal inference: E-value or unmeasured-confounding sensitivity?
```

Each item → 🟢/🟡/🔴 with rewriting suggestion

---

## Mode 6 — Reviewer 2 Simulation (adversarial)

จำลอง reviewer ที่:
- เป็น expert ในสาขาเดียวกัน
- เคยทำงานเรื่องเดียวกัน (potential rival)
- อ่านเร็วและจับจุดอ่อน
- มักขอ revision หนัก

Output:
```
"Reviewer 2" comments (simulated):

Major:
1. The authors' definition of [variable] differs from the standard
   used by [Smith 2020]. Please justify the choice or provide
   sensitivity analyses using both definitions.
2. The PH assumption is not tested; given long follow-up, this is
   essential. Please provide Schoenfeld residual plots.
3. ...

Minor:
1. Several typos in Table 2 column headers.
2. Reference 14 is mis-cited — it does not address [claim].
3. ...
```

---

## Output Files

1. `deep_review_<date>.md` — full 12-criteria report
2. `reviewer_anticipation_<date>.xlsx` — objection matrix
3. `revision_plan_<date>.xlsx` — prioritized action list
4. `reporting_guideline_compliance_<date>.xlsx` — checklist
5. `stats_scrutiny_<date>.md` — biostatistician review

---

## หลักการสำคัญ

- **เป็น Reviewer 2 ที่ดี** — ตรงไปตรงมา, สร้างสรรค์, มีหลักฐาน
- **อย่า** ตี paper แบบ ad hominem
- **ต้อง** ให้ specific fixes — ไม่ใช่แค่ "improve writing"
- **ต้อง** อ้างอิง guideline/reference ในทุก major comment
- **ต้อง** แยก major / minor อย่างชัดเจน
- ผู้วิจัยใช้ผลนี้ก่อน submit → ลด revision rounds จริง

---

## Companion Skills

- `manuscript-writer` (Mode 6 wrapper เรียกใช้ skill นี้)
- `response-to-reviewers` (หลัง revise actual reviewer comments)
- `manuscript-master-workflow` Mode B (peer review 7 หมวด — สามารถใช้ร่วมได้)
