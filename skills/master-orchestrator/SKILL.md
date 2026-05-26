---
name: master-orchestrator
description: >
  ใช้ skill นี้เมื่อผู้ใช้เริ่มงานวิจัยใหม่หรือไม่แน่ใจว่าจะเริ่มต้นที่ skill ใด เช่น
  "เริ่มงานวิจัย", "publish in Q1", "start manuscript pipeline", "ตีพิมพ์ Q1 Q2",
  "high impact factor publication", "วางแผนงานวิจัยตั้งแต่ต้น", "ทำงานวิจัยตั้งแต่ data ไปจน submit",
  "ทำตามขั้นตอน", "end to end", "ไม่รู้จะเริ่มยังไง", "orchestrate", "pipeline mode",
  "ส่ง Q1", "publish workflow", "research project from scratch", "วางแผน manuscript",
  "เลือก skill ไหนดี", "ช่วยแนะนำขั้นตอน", "routing", "เริ่มจากตรงไหน"
metadata:
  version: "1.0.0"
  author: "Chagkrit"
  target_tier: "Q1 high-impact-factor"
---

# Master Orchestrator — End-to-End Q1/Q2 Pipeline
# ระบบประสานงานหลักของ Medical Research Pipeline Plugin

## บทบาท
เป็น **entry point** และ **router** สำหรับ Medical Research Pipeline Plugin
แนะนำผู้ใช้ตั้งแต่ขั้นตอนแรก ไปจนถึงการตอบ reviewer หลังตีพิมพ์
ใช้ skill ที่เหมาะสมในแต่ละจังหวะ — ไม่ให้ผู้ใช้หลงทาง

---

## Pipeline Overview (8 Stages)

```
┌──────────────────────────────────────────────────────────────┐
│              MASTER ORCHESTRATOR (this skill)                 │
└──────────────────────────────────────────────────────────────┘

  Stage 1  PROJECT INTAKE       — clarify aims, design, target tier
  Stage 2  DATA PREP            → data-prep skill
  Stage 3  STATISTICAL ANALYSIS → medical-stats skill
  Stage 4  MANUSCRIPT DRAFTING  → manuscript-writer skill
  Stage 5  INTERNAL PEER REVIEW → peer-review skill
  Stage 6  JOURNAL STRATEGY     → journal-strategy skill
  Stage 7  SUBMISSION PACKAGE   → submission-package skill
  Stage 8  RESPONSE TO REVIEWER → response-to-reviewers skill
                                  (loop back to Stage 4/3/2 as needed)
```

---

## Stage 1 — Project Intake (always start here)

ถามข้อมูลพื้นฐาน (ทีละกลุ่ม ไม่ overwhelm):

### Block A — Research design
```
1. ประเภทงาน: RCT / Cohort / Case-Control / Cross-sectional /
   Diagnostic accuracy / Prediction model / Systematic review /
   Case report / Qualitative
2. PICO/PECO: Population / Intervention or Exposure / Comparator / Outcome
3. Setting: country, period, single vs multi-center
4. Sample size (approximate)
5. Pre-registered? (PROSPERO / clinicaltrials.gov / OSF) — ID
6. Reporting guideline: CONSORT / STROBE / STARD / TRIPOD /
   PRISMA / CARE / SRQR / ARRIVE
```

### Block B — Publication target
```
7. Target tier: top-tier Q1 (NEJM/Lancet/JAMA) / specialty Q1/Q2 / open
8. Plan A journal (ถ้ามี)
9. Funder mandates / OA requirement / APC budget
10. Time pressure / deadline
```

### Block C — Current state
```
11. Stage ที่อยู่ตอนนี้:
    a. มี data raw — ยังไม่ได้ clean
    b. clean แล้ว — ยังไม่ได้วิเคราะห์
    c. มีผลสถิติแล้ว — ยังไม่ได้เขียน
    d. มี draft แล้ว — ต้องการ review
    e. ถูก reject / R&R — ต้องตอบ reviewer
    f. อื่นๆ
```

จากข้อมูลข้างต้น → **โหลด pipeline plan**:

---

## Auto-route to Stage

| Current state (Q11) | Next skill |
|---|---|
| (a) raw data | `data-prep` Mode 0 → 3 |
| (b) clean data | `medical-stats` (auto-detect Track A–G) |
| (c) stats done | `manuscript-writer` Mode 1 |
| (d) draft exists | `peer-review` Mode 1 → `manuscript-writer` Mode 4 QC |
| (e) reviewer comments | `response-to-reviewers` Mode 1 → 2 |
| (f) other | ถามรายละเอียดเพิ่ม |

---

## Stage 2 — Data Preparation (delegate)
→ `data-prep` skill

ผลลัพธ์ที่ orchestrator คาดหวัง:
- `data_clean_<date>.xlsx` (analysis dataset)
- `data_dictionary_<date>.xlsx` (codebook)
- `flow_diagram_numbers.txt` (CONSORT/STROBE counts)
- `audit_trail.md`
- `methods_data_paragraph.txt`

→ pass ไปยัง Stage 3 เมื่อ user confirm

---

## Stage 3 — Statistical Analysis (delegate)
→ `medical-stats` skill

ผลลัพธ์:
- `table1.xlsx`
- `table2_outcomes.xlsx`
- `table3_multivariable.xlsx`
- `figures/*.tiff` (≥300 DPI, STATA-style)
- `statistical_methods.txt`
- `results_paragraph.txt`
- `sap_summary.md`
- `sensitivity_report.xlsx`

→ pass ไปยัง Stage 4

---

## Stage 4 — Manuscript Drafting (delegate)
→ `manuscript-writer` skill

ผลลัพธ์:
- `manuscript_R0_draft.docx` (English, IMRAD)
- `tables.docx`
- `figures/*.tiff` (linked)
- `supplementary.pdf`
- `reporting_guideline_compliance.xlsx`

→ pass ไปยัง Stage 5

---

## Stage 5 — Internal Peer Review (delegate)
→ `peer-review` skill (Mode 1 ทุกครั้ง)

ถ้าผลออกมามี 🔴 → loop back to Stage 4 (revise)
ถ้าผลออกมามี 🟡 เป็นส่วนใหญ่ + 🟢 — ให้ user เลือก revise หรือ submit

→ pass ไปยัง Stage 6

---

## Stage 6 — Journal Strategy (delegate)
→ `journal-strategy` skill

ผลลัพธ์:
- `journal_strategy.xlsx` (Plan A/B/C with metrics)
- `cover_letter_<journalA>.docx`
- `scope_fit_matrix.xlsx`

→ pass ไปยัง Stage 7

---

## Stage 7 — Submission Package (delegate)
→ `submission-package` skill

ผลลัพธ์: `/submission_<journal>_<date>/` ครบทุกไฟล์

→ user submit; รอ decision

---

## Stage 8 — Response to Reviewers (delegate, if needed)
→ `response-to-reviewers` skill

ผลลัพธ์:
- `response_letter_R1.docx`
- `editor_summary_R1.docx`
- `revised_manuscript_R1_clean.docx`
- `revised_manuscript_R1_tracked.docx`

→ resubmit → wait → if R2 needed, loop

---

## Status Board (แสดงให้ user ทุกครั้งที่กลับมาที่ orchestrator)

```
═══════════════════════════════════════════════════════════════════
🧭 RESEARCH PIPELINE STATUS
═══════════════════════════════════════════════════════════════════
Project: <title>
Design : <design>           Target: <Plan A journal>  IF: <n>
Stage  : <current stage> / 8

   ✅  1. Project Intake
   ✅  2. Data Preparation
   ✅  3. Statistical Analysis
   🔄  4. Manuscript Drafting       ← current
   ⏳  5. Internal Peer Review
   ⏳  6. Journal Strategy
   ⏳  7. Submission Package
   ⏳  8. Response to Reviewers

Next action: เขียน Discussion paragraph 2 (Context — comparison
             with prior literature). Suggest: run citation search
             first (manuscript-writer Mode 5).
═══════════════════════════════════════════════════════════════════
```

---

## Pipeline Quality Gates (must pass to advance)

| Stage | Gate |
|---|---|
| 2 → 3 | Audit trail complete; missing strategy documented; analysis dataset locked |
| 3 → 4 | All Q1-required outputs present; assumption checks passed or addressed |
| 4 → 5 | Reporting-guideline compliance ≥80%; consistency QC passed |
| 5 → 6 | No 🔴 in 12-criteria review |
| 6 → 7 | Plan A/B/C confirmed; cover letter draft accepted by user |
| 7 → submit | Pre-submission checklist 100% complete |
| 8 → resubmit | All reviewer comments addressed; track-changes paired with clean version |

---

## Companion Skill — manuscript-master-workflow

ถ้า user ติดตั้ง `manuscript-master-workflow` skill:
- Stage 4 มี option ไป **Mode A** (deep development)
- Stage 5 มี option ไป **Mode B** (7-section peer review เสริมจาก 12-criteria)
- Stage 5 มี option ไป **Mode C** (academic English refinement)
- Stage 6 มี option ไป **Mode D** (journal selection deep-dive)

orchestrator จะตรวจ availability และเสนอใช้

---

## Hard rules (Q1 High-IF mode)

1. **อย่า** ข้าม Stage — ทุก gate ต้องผ่าน
2. **อย่า** ใช้ default tier ต่ำกว่า Q1 high-IF ยกเว้น user เลือก
3. **ต้อง** ทำ peer-review skill (Stage 5) ก่อนทุก submission
4. **ต้อง** มี audit trail ตลอดทุก stage
5. **ต้อง** keep status board ให้ทันสมัย

---

## Output (orchestrator-level)

`research_project_log.md` — บันทึกทุก stage transition, decision, output file path
→ reproducibility อย่างเต็มรูปแบบ
