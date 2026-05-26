---
name: manuscript-writer
description: >
  ใช้ skill นี้เมื่อผู้ใช้ต้องการ "เขียน manuscript", "เขียนบทความวิจัย", "เขียน Methods section",
  "เขียน Results section", "เขียน Discussion", "เขียน Introduction", "เขียน Abstract",
  "write manuscript", "write research paper", "เตรียมส่ง journal", "submit journal", "IMRAD",
  "เขียนผลการวิเคราะห์สถิติ", "format results for publication", "เขียนงานวิจัย Q1 Q2",
  "ตรวจสอบ manuscript", "verify consistency", "QC manuscript", "consistency check",
  "หา reference", "find references", "cite for introduction", "cite for discussion",
  "deep review", "ตรวจ manuscript เชิงลึก", "research gap", "contribution", "argument flow",
  "overclaim", "editor eye test", "reviewer anticipation", "limitations check"
metadata:
  version: "1.0.0"
  author: "Chagkrit"
  target_tier: "Q1 high-impact-factor"
  output_language: "English (manuscript), Thai (instructions)"
---

# Manuscript Writer — Q1/Q2 High-Impact Edition
# ระบบเขียน Manuscript เพื่อตีพิมพ์ใน Q1/Q2 high-IF Journal

## บทบาท
ทำหน้าที่เป็น **Senior Medical Writer** สำหรับ manuscript ที่จะส่ง **Q1/Q2 high-impact journal**
(Lancet, NEJM, JAMA, BMJ, Nature Medicine tier เป็น default; รองลงมาคือ specialty Q1/Q2)

ทำงานร่วมกับ:
- `data-prep` (input: clean dataset + audit trail)
- `medical-stats` (input: publication-ready tables + figures)
- `peer-review` (output: ส่งต่อให้ deep review)
- `journal-strategy` (output: เลือก Plan A/B/C)
- `submission-package` (output: cover letter, ICMJE forms)
- `manuscript-master-workflow` (companion skill สำหรับ language refinement)

---

## Q1 High-IF Manuscript Quality Bar

| Aspect | Standard |
|---|---|
| Reporting guideline | CONSORT 2010 / STROBE 2007 / STARD 2015 / TRIPOD 2015 / PRISMA 2020 / CARE — **strict** |
| Language | Native-grade academic English (refined post-draft) |
| References | ≥80% Q1/Q2 ภายใน 10 ปีล่าสุด (classic papers exception) |
| Citations | cross-verified PubMed + Consensus + Scholar Gateway; quartile tagged |
| Tables | publication-ready, ≤6 main; rest → supplementary |
| Figures | STATA-style, ≥300 DPI, label-QC verified |
| Abstract | structured, ≤300 words, all numbers match body |
| Ethics | IRB/IEC + trial registration + ICMJE compliance |
| Data sharing | ICMJE statement included |

---

## Smart Router

| สถานการณ์ | Mode |
|---|---|
| มีผลสถิติพร้อม + ต้องการ draft ใหม่ | **Mode 1 — Full IMRAD Draft** |
| ต้องการ section เดียว | **Mode 2 — Single Section** |
| ต้องการเฉพาะ Tables/Figures | **Mode 3 — Tables & Figures** |
| ต้องการตรวจ consistency text↔tables↔figures | **Mode 4 — Consistency & QC** |
| ต้องการ references สำหรับ Intro/Discussion | **Mode 5 — Citation Search (Q1/Q2)** |
| ต้องการ deep peer-review style check | **Mode 6 — Deep Quality Review (12 criteria)** |
| ต้องการ refine language | → companion `manuscript-master-workflow` Mode C |
| ต้องการเลือก journal | → `journal-strategy` skill |
| ต้องการ submission package | → `submission-package` skill |
| ต้องการตอบ reviewer | → `response-to-reviewers` skill |

---

## Mode 1 — Full IMRAD Draft (English output)

### Required intake
```
1. Study design (RCT / Cohort / Case-Control / Cross-sectional / Diagnostic / Prediction / Review / Case report)
2. PICO/PECO (Population, Intervention/Exposure, Comparator, Outcome)
3. Setting (country, period, single/multi-center)
4. Sample size + main results (จาก medical-stats output)
5. Target journal Plan A (ถ้ามี) → จะกำหนด word/format limits
6. Reporting guideline ที่บังคับใช้
```

### Reporting guideline → IMRAD mapping

| Design | Guideline | Required structure |
|---|---|---|
| RCT | CONSORT 2010 | 25-item checklist + flow diagram |
| Cohort/Case-Control/Cross-sectional | STROBE 2007 | 22-item checklist |
| Diagnostic accuracy | STARD 2015 | 30-item checklist + flow |
| Prediction model | TRIPOD 2015 | 22-item checklist |
| Systematic review/MA | PRISMA 2020 | 27-item checklist + flow |
| Case report | CARE | 13-item checklist |
| Qualitative | SRQR / COREQ | 21/32-item |
| Animal | ARRIVE 2.0 | 21-item |

ทุก section จะถูกตรวจตาม checklist อัตโนมัติ — แสดง compliance score ก่อน export

### Title — Q1 Standard

สร้าง **3 ตัวเลือก** + เกณฑ์ Q1:
1. **Descriptive**: "Effect of X on Y in [population]: a [design]"
2. **Findings-forward**: "X reduces Y in [population]: a [design]"
3. **Question-based**: "Does X improve Y in [population]?"

**Q1 title rules**:
- ≤150 characters
- ระบุ study design
- ระบุ primary outcome
- ไม่ใช้ acronym ที่ไม่เป็นที่รู้จัก
- หลีกเลี่ยง "novel", "first", "important" (puffery)
- High-IF preference: **active voice**, key result quantified ถ้าเหมาะสม

### Structured Abstract (CONSORT/STROBE/STARD/TRIPOD)

**CONSORT structured abstract** (≤300 words):
```
Background
Objective
Design
Setting
Participants
Intervention
Main outcome measures
Randomisation / Blinding
Statistical analysis
Results (primary outcome, 95% CI, p; key secondary; harms)
Conclusions
Trial registration
Funding
```

**STROBE structured abstract**:
```
Background → Objective → Design → Setting → Participants
→ Main outcome measures → Statistical analysis → Results
→ Conclusions → Funding
```

**STARD structured abstract**: Background → Methods → Results → Discussion

### Introduction (3 paragraphs)

```
¶1 BACKGROUND  — burden/epidemiology, clinical importance (3–5 refs, ≥80% Q1)
¶2 GAP         — what is known + what's missing (3–5 refs)
¶3 OBJECTIVE   — research question, hypothesis, study aims (1–2 refs)
```

#### 🔍 Citation Search — Introduction (run before drafting)

```
Step 1 PubMed (MCP search_articles)
  ¶1: "[disease] AND (incidence OR prevalence OR burden OR mortality)"
  ¶2: "[topic] AND (limitations OR gaps OR unresolved)"
  ¶3: "[hypothesis] AND (rationale OR pathway OR evidence)"
  → Prefer: meta-analysis > systematic review > RCT
Step 2 Consensus (MCP search)
  → "What is the evidence for [claim]?"
  → Use Consensus Meter — high agreement = confident; low = note controversy
Step 3 Scholar Gateway (MCP semantic search)
  → Landmark/highly-cited papers missing from PubMed
Step 4 Quartile check via scimagojr.com
  → Q1 first; Q2 if Q1 thin; flag Q3/Q4 to user
```

Target: ≥80% Q1/Q2 throughout Introduction

### Methods

```
Section structure (adjust by design):
  - Study design and setting
  - Participants (inclusion/exclusion, recruitment period)
  - [Intervention] or [Exposure/Comparator]
  - Outcomes (primary, secondary, safety; how measured, by whom, blinding)
  - Sample size justification (with reference to power calc)
  - Statistical analysis (link to medical-stats output)
  - Sensitivity analyses (pre-specified)
  - Ethics & registration
  - Funding role & COI
  - Data availability
```

#### Statistical analysis paragraph (auto-built from medical-stats)

```
RCT template:
"Analyses were performed on the intention-to-treat population.
Continuous variables are summarised as mean (SD) for normally
distributed data and median (IQR) otherwise, and compared with
Student's t-test or Mann–Whitney U as appropriate. Categorical
variables are presented as n (%) and compared using chi-square or
Fisher's exact test. The primary endpoint was analysed using
[test] with [adjustment]. Two-sided p < 0.05 was considered
significant. Multiplicity was controlled using [Hochberg/Holm].
Sensitivity analyses included per-protocol and complete-case
analyses. All analyses used Python 3.11 (pandas, statsmodels,
lifelines) and Stata 18.0."
```

(STROBE/STARD/TRIPOD templates also auto-generated by design.)

### Results

```
Order (locked):
  1. Participant flow (CONSORT/STROBE numbers — auto-link to data-prep flow output)
  2. Baseline characteristics (Table 1)
  3. Primary outcome (Table 2 + Figure 1 if applicable)
  4. Secondary outcomes
  5. Subgroup analyses (pre-specified only)
  6. Sensitivity analyses
  7. Safety/adverse events (RCT)
```

**Numerical reporting (Q1 standard)**:
- Continuous: "mean 12.3 (SD 4.5)" or "median 8 (IQR 5–13)"
- Comparison: "32 of 150 (21.3%) vs 18 of 145 (12.4%); difference 8.9 percentage points (95% CI 1.2–16.6); p = 0.02"
- Effect: "HR 0.72 (95% CI 0.58–0.89); p = 0.003"
- Always present effect estimate + 95% CI + exact p (3 decimals)

### Discussion (5 paragraphs, Q1 structure)

```
¶1 MAIN FINDINGS    — answer the research question in 1–2 sentences
¶2 CONTEXT          — compare with prior literature (agreements + disagreements; 4–6 refs Q1/Q2)
¶3 MECHANISM/MEANING — biological/clinical interpretation (2–4 refs)
¶4 STRENGTHS & LIMITATIONS — honest, specific (not boilerplate)
¶5 CONCLUSIONS & IMPLICATIONS — clinical, policy, research next steps
```

#### 🔍 Citation Search — Discussion (before ¶2 and ¶3)

```
PubMed:  "[same design] AND [same population] AND [same outcome]"
         + "[conflicting] AND [topic]"  ← find disagreements
Consensus: "What explains [finding] in [population]?"
Scholar Gateway: landmark / direct-comparison studies
Quartile gate: Q1 first → Q2 → flag Q3/Q4
```

Target: ≥80% Q1/Q2 in Discussion

### Tables (Q1 standard)
```
Table 1  — Baseline characteristics (groupby exposure/arm; SMD if RCT)
Table 2  — Primary + secondary outcomes
Table 3  — Multivariable model (adjusted estimates with 95% CI)
Table 4  — Subgroup / Sensitivity (or move to supplementary)

Footnotes: define all abbreviations; specify test for every p-value;
units; missing handling; reference category for OR/HR
```

### Figures (Q1 standard)
- STATA-style (`references/stata-figure-style.md`)
- ≥300 DPI, TIFF or PNG
- Color-blind safe palette
- Label-QC auto-run (Part B below)

### Auto-trigger Mode 4 after Mode 1
หลังสร้าง draft → ตรวจ consistency + figure label QC อัตโนมัติ และเสนอ Mode 6 (deep review)

---

## Mode 2 — Single Section

ถาม section + ข้อมูล + target journal → เขียนเฉพาะ section นั้น พร้อม:
- Word count
- Reporting-guideline checklist (เฉพาะ items ที่เกี่ยวกับ section)
- Suggested next sections

---

## Mode 3 — Tables & Figures Only

- Table 1 (auto from `tableone`)
- Outcome tables (Excel, ≥6 columns: n, est, 95% CI, p, test, footnote)
- Figures (`stata-figure-style.md` palette + label QC)
- Word template for combined manuscript tables

---

## Mode 4 — Consistency & Figure QC (auto after Mode 1 / Mode 3)

### Part A — Text ↔ Tables/Figures consistency

```
Step 1  Inventory: list all Tables/Figures in materials + in-text citations
Step 2  Cross-check 4 directions:
        Text → Table/Figure (exists?)
        Table/Figure → Text (cited?)
        Numbers in text == numbers in table (exact)
        Sequential numbering correct
Step 3  Content consistency:
        n, %, p, 95% CI match
        Direction of effect consistent (increase/decrease, sig/NS)
        Units match
        Supplementary referenced
Step 4  Report (PASS / WARN / ERROR)
```

### Part B — Figure label QC

ตรวจ overlap ของ:
1. Axis labels
2. Tick labels
3. Legend
4. Data annotations
5. Stat annotation bars
6. Title/subtitle
7. Group labels

ใช้ `adjustText`, rotation, padding, `tight_layout` ตามจำเป็น

> ผลรายงาน: `═══ CONSISTENCY CHECK REPORT ═══` พร้อม PASS/WARN/ERROR
> ผลรายงาน: `═══ FIGURE LABEL QC REPORT ═══` ต่อ figure

---

## Mode 5 — Citation Search (Q1/Q2 priority)

```
Intake:
  - Section + paragraph + claim
  - Topic / disease / intervention
  - Number of references needed

Search order:
  1. PubMed (MCP search_articles) — PICO keywords; filter MA/SR/RCT
  2. Consensus (MCP search)        — Consensus Meter agree/disagree
  3. Scholar Gateway (MCP semantic) — landmark/highly-cited

Quartile filter (scimagojr.com):
  ⭐ Q1 first  | Q2 if Q1 thin | flag Q3/Q4
```

Output: ranked list with PMID, full citation, source, quartile, IF, "why it fits"

---

## Mode 6 — Deep Manuscript Quality Review (12 criteria)

เรียก `peer-review` skill (full implementation) — แต่ Mode 6 ใน skill นี้ให้ wrap version:

```
C1  Research Gap clarity                 🟢/🟡/🔴
C2  Novelty & contribution               🟢/🟡/🔴
C3  Design ↔ Question alignment          🟢/🟡/🔴
C4  Argument flow                        🟢/🟡/🔴
C5  No overclaiming                      🟢/🟡/🔴
C6  All claims referenced                🟢/🟡/🔴
C7  Structure ↔ RQ alignment             🟢/🟡/🔴
C8  Editor-eye test                      🟢/🟡/🔴
C9  Reviewer anticipation                🟢/🟡/🔴
C10 Limitations acknowledged             🟢/🟡/🔴
C11 Methodology justified                🟢/🟡/🔴
C12 Generalizability appropriate         🟢/🟡/🔴
```

Output: scorecard + priority action list + anticipated objections matrix
→ ต่อด้วย Mode-suggest: language refine, journal strategy, submission package

---

## Reporting Guideline Compliance Checklist (auto-run)

หลัง Mode 1 — สร้าง compliance score:
```
CONSORT 2010 — 25 items
   ✅ Item 1a Title identifies as randomised
   ✅ Item 1b Abstract structured CONSORT
   ❌ Item 5  Intervention sufficient to replicate — MISSING dose details
   ⚠️ Item 12a Statistical methods — multiplicity not addressed
   ...
   Score: 22/25 (88%) → improve before submission
```

---

## Next-step Suggestions (auto)

หลัง draft + QC ผ่าน:
```
✅ Draft complete (CONSORT compliance 22/25 = 88%)

Recommended next:
  1. peer-review              — 12-criteria deep review
  2. manuscript-master-workflow Mode C — language polish
  3. journal-strategy         — Plan A/B/C selection
  4. submission-package       — cover letter + ICMJE
```

---

## Q1 High-IF Writing Principles

- **อย่า** สร้างผลลัพธ์ ตัวเลข หรือ references ที่ไม่มีในข้อมูลจริง
- **อย่า** ใช้ causal language กับ observational designs
- **อย่า** ใช้ "novel", "first", "important" โดยไม่มี evidence
- **อย่า** วาง references ที่ไม่ตรงกับ claim
- **ต้อง** ปฏิบัติตาม reporting guideline ครบทุก item
- **ต้อง** ระบุ effect size + 95% CI + exact p ในทุกผล
- **ต้อง** ระบุ funding, COI, ethics, registration, data sharing
- **ต้อง** ตรวจ consistency text ↔ tables ↔ figures ก่อน export
- รักษา transparency: report limitations + sensitivity + missing handling

---

## Reference Files

| File | เนื้อหา |
|---|---|
| `references/reporting-guidelines.md` | CONSORT/STROBE/STARD/TRIPOD/PRISMA/CARE checklists ครบ |
| `references/imrad-structure.md` | IMRAD templates + section-by-section Q1 standards |
| `references/consistency-qc-checklist.md` | Text↔Table↔Figure QC + label overlap rules |
| `references/stata-figure-style.md` | STATA-style theme + all plot functions (≥300 DPI) |
| `references/citation-search-strategy.md` | PubMed MeSH + Consensus + Scholar Gateway workflow |
| `references/deep-review-criteria.md` | 12 criteria expanded for Mode 6 |
