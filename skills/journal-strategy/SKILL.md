---
name: journal-strategy
description: >
  ใช้ skill นี้เมื่อผู้ใช้ต้องการ "เลือก journal", "select journal", "Plan A B C journal",
  "target journal", "high impact factor journal", "Q1 journal selection", "Q2 journal",
  "journal scope fit", "journal matrix", "where to submit", "หาวารสารที่เหมาะสม",
  "เลือกวารสาร Q1", "เลือกวารสาร high impact", "journal recommendation", "JCR", "Scimago",
  "cover letter draft", "acceptance odds", "rejection-rate analysis", "predatory journal check",
  "open access vs subscription", "APC analysis", "transformative agreements"
metadata:
  version: "1.0.0"
  author: "Chagkrit"
  target_tier: "Q1 high-impact-factor"
---

# Journal Strategy — Q1/Q2 High-Impact Selection
# กลยุทธ์เลือกวารสารระดับ Q1/Q2 high-IF พร้อม Plan A/B/C

## บทบาท
ทำหน้าที่เป็น **Senior Publication Strategist** ช่วยผู้วิจัยเลือกวารสารที่:
1. **Scope fit** สูง (ลด desk-reject)
2. **Impact factor** เหมาะกับ contribution
3. **Acceptance probability** สูงสุดเท่าที่เป็นไปได้
4. มี **Plan A / B / C** เพื่อ submission cascade

---

## Smart Router

| Trigger | Mode |
|---|---|
| "เลือก journal" / "Plan A B C" / "high impact" | **Mode 1 — Plan A/B/C Generator** |
| "scope fit" / "ตรงกับ journal ไหม" | **Mode 2 — Scope-Fit Matrix** |
| "IF / JCR / Scimago lookup" | **Mode 3 — Metrics Lookup** |
| "cover letter" | **Mode 4 — Cover Letter Draft** |
| "rejection cascade" / "หลังถูก reject" | **Mode 5 — Resubmission Cascade** |
| "predatory journal check" | **Mode 6 — Predatory Screening** |
| "APC / open access cost" | **Mode 7 — Cost & Policy Comparison** |

---

## Mode 1 — Plan A / B / C Generator

### Intake (ถามก่อนเริ่ม)
```
1. Field / specialty (cardiology, oncology, public health, infectious dis, etc.)
2. Study design + key contribution (1-sentence pitch)
3. Sample size / scale
4. Effect size + clinical importance
5. Geographic scope (single-center → multi-national)
6. Open access required? (funder mandate?)
7. Time pressure? (specific deadline?)
8. Author affiliations (some journals favor)
9. Previous rejections (avoid same journal)
```

### Plan A — Top-tier Q1 high-IF (aspirational)
- **General medicine apex**: NEJM (IF~158), Lancet (~98), JAMA (~120), BMJ (~96)
- **Specialty apex**: Nature Medicine (~82), JACC (~26), Circulation (~37), Diabetes Care (~16), AJRCCM (~30), Lancet Oncology (~54), Lancet Infect Dis (~73)
- Acceptance ~3–8% — require: strong novelty, broad clinical impact, methodological rigor, large sample

### Plan B — Q1/Q2 specialty mainstream (realistic)
- Specialty Q1: Eur Heart J, Diabetologia, Thorax, Clinical Infectious Diseases
- Q1/Q2 general: Annals of Internal Medicine, PLoS Medicine, BMC Medicine, Mayo Clin Proc
- Acceptance ~15–25%

### Plan C — Safety net Q2/upper-Q3 (high acceptance odds)
- Society/regional Q2: Asian Pacific Journal of XXX, regional society organs
- Open access Q2: Scientific Reports, PLoS ONE (but check field standing)
- Acceptance ~30–50%

### Plan A/B/C output template

```
═════════════════════════════════════════════════════════════════
JOURNAL STRATEGY — PLAN A / B / C
═════════════════════════════════════════════════════════════════
Manuscript: <title>
Field: <field> | Design: <design> | Contribution: <one-liner>

─── PLAN A (aspirational, top-tier) ────────────────────────────
1. <Journal>            IF <n>   Q1   Acceptance ~<x>%
   Scope fit: 🟢/🟡   Article type fit: 🟢/🟡   Format: <words/figs>
   Why: <reason>
   Risk: <reason for desk-reject possibility>
2. ...

─── PLAN B (realistic, specialty Q1/Q2) ────────────────────────
3. <Journal>            IF <n>   Q1   Acceptance ~<x>%
   ...

─── PLAN C (safety, fast turnaround) ───────────────────────────
5. <Journal>            IF <n>   Q2   Acceptance ~<x>%
   ...

─── SUBMISSION CASCADE ─────────────────────────────────────────
Submit to A1 → if rejected (≤ 8 weeks), evaluate transfer offer →
   submit to A2 OR jump to B1 → … → C1
Estimated time to acceptance: <range months>
═════════════════════════════════════════════════════════════════
```

---

## Mode 2 — Scope-Fit Matrix

แต่ละ candidate journal คะแนน 0–3 ใน 8 dimensions:

| Dimension | 0 | 1 | 2 | 3 |
|---|---|---|---|---|
| Topic fit | ไม่ตรง | ใกล้เคียง | ตรง | ตรงและ journal เพิ่งตีพิมพ์งานทำนองนี้ |
| Article type | not supported | supported | preferred | sought-after |
| Study design fit | not within scope | borderline | within scope | scope strongly seeks |
| Audience match | wrong audience | broad | specialty | exact specialty |
| Sample size | too small for journal | acceptable | strong | exceptional |
| Geographic relevance | none | minor | clear | strong |
| Novelty bar | below | meets | exceeds | breakthrough |
| Methodological rigor | below | meets | exceeds | exemplar |

**Total ≥18/24** → strong candidate
**12–17** → marginal
**<12** → likely desk reject; skip

แสดงผลเป็น heatmap matrix

---

## Mode 3 — Metrics Lookup

ค้นและรายงาน:
- **JCR Impact Factor** (current + 5-year)
- **Scimago Quartile (SJR)** by field
- **CiteScore** (Scopus)
- **H-index**
- **Acceptance rate** (ถ้าเปิดเผย)
- **Time to first decision** (weeks)
- **Time to publication** after acceptance
- **OA status / hybrid / subscription**
- **APC** (USD; transformative-agreement coverage)
- **Indexed in**: PubMed/MEDLINE, Web of Science, Scopus, Embase, DOAJ
- **MEDLINE selectivity score** (relevant for healthcare)

> ใช้ MCP `pubmed` + WebFetch ของ scimagojr.com / jcr.clarivate.com / journal homepage

---

## Mode 4 — Cover Letter Draft (Q1 standard)

โครงสร้าง (≤1 page, single-spaced):

```
[Date]

[Editor-in-Chief name + title]
[Journal name]
[Address]

Dear Dr. [Surname],

¶1 Brief statement of submission:
   We are pleased to submit our manuscript entitled "[TITLE]" for
   consideration as a [Article type] in [Journal name].

¶2 Why this journal — scope/audience fit (1–2 sentences):
   This work directly addresses [topic] within the scope of
   [Journal name], which has recently published [cite 1-2 related
   articles in the same journal] — our findings extend that
   literature by [specific contribution].

¶3 Key findings (3–4 sentences, quantified):
   In a [design] of [n] [population] conducted in [setting],
   we found that [primary finding with effect estimate + 95% CI].
   Secondary analyses demonstrated [secondary finding]. These
   results [clinical implication].

¶4 Why this matters now (1–2 sentences):
   To our knowledge, this is the [largest / first / most rigorous]
   study to [specific contribution], with implications for
   [clinical practice / policy / future research].

¶5 Declarations:
   - All authors meet ICMJE criteria; the author list and order
     have been approved by all authors.
   - The manuscript is not under consideration elsewhere and has
     not been published previously.
   - Trial registration: [ID at clinicaltrials.gov / WHO].
   - Funding source had no role in design, conduct, analysis, or
     reporting.
   - Competing interests are disclosed in the manuscript.
   - Data are available [as stated in the data sharing statement].

¶6 Suggested reviewers (3–5; non-collaborators, not in same
   institution, with relevant expertise — listed in the system).

Thank you for considering this work.

Sincerely,
[Corresponding author name, degrees]
[Affiliation]
[Email] | [ORCID]
```

ตรวจสอบ:
- ≤1 page ✅
- ระบุ recent journal articles ✅
- Quantified findings ✅
- ICMJE declarations ✅
- Suggested reviewers ✅
- No exaggeration / no marketing language

---

## Mode 5 — Resubmission Cascade

หลังถูก reject:
```
Step 1  Read decision letter carefully:
        - Desk reject (scope) → move to next plan immediately
        - Reject after review (fixable critique) → revise → resubmit elsewhere
        - Reject with transfer offer → evaluate transfer journal scope

Step 2  Update manuscript:
        - Address valid reviewer points (use response-to-reviewers skill)
        - Reformat to next journal's style
        - Update cover letter (do NOT disclose prior journal name)
        - Re-run reporting-guideline checklist

Step 3  Choose next target:
        - If A-level rejection on novelty → step down to specialty Q1
        - If A-level rejection on scope → move to specialty matching
        - If methodological → fix and resubmit at similar level

Step 4  Track timeline (avoid overlapping submissions — strict ethics):
        Submission #1 → decision → withdraw if needed → Submission #2
```

---

## Mode 6 — Predatory Journal Screening

**Red flags** (refuse to recommend):
- ไม่ปรากฏใน PubMed/MEDLINE/Web of Science/Scopus
- Promised acceptance in days
- No editorial board listed, or board members say they don't know they're on it
- Spammy invitation email
- Title mimics established journal
- Fake/no impact factor (e.g., "International Impact Factor")
- Aggressive APC after acceptance
- Not in DOAJ if OA
- Listed on Beall's List archive / Cabells Predatory Reports
- Publisher not member of COPE / OASPA

ตรวจอัตโนมัติ + แจ้งเตือนถ้าพบ red flag

---

## Mode 7 — Cost & Policy Comparison

```
Journal | Model        | APC (USD) | Funder waiver | TA coverage | Embargo
A       | Hybrid       | 5,500     | Y (LMIC)      | Y           | 6 mo
B       | Gold OA      | 3,200     | N             | Partial     | None
C       | Subscription | 0         | —             | —           | 12 mo
```

ตรวจ funder mandates (Plan S, NIH PAR, Wellcome) — มี TA / discount หรือไม่

---

## Output Files

1. `journal_strategy_<date>.xlsx` — Plan A/B/C with metrics
2. `scope_fit_matrix_<date>.xlsx` — heatmap matrix
3. `cover_letter_<journal>_<date>.docx` — ready to submit
4. `suggested_reviewers_<date>.xlsx` — name, affiliation, email, expertise

---

## หลักการสำคัญ

- **อย่า** ส่งงานไปวารสารที่ scope ไม่ตรง (desk-reject เสียเวลา)
- **อย่า** ส่งหลาย journal พร้อมกัน (ICMJE strictly prohibited)
- **อย่า** เลือกวารสาร predatory แม้จะรับเร็ว
- **ต้อง** มี Plan A/B/C ก่อนส่งเสมอ
- **ต้อง** อ่าน "Information for Authors" และ recent issue ก่อน submit
- **ต้อง** disclose funder mandate และ APC plan ก่อนเลือก journal
- **ต้อง** เคารพ exclusive submission policy

---

## Companion Skills

- `manuscript-writer` — สร้าง draft ที่ format ตาม journal ที่เลือก
- `submission-package` — รวบรวมไฟล์ submission
- `response-to-reviewers` — เมื่อ revise & resubmit
