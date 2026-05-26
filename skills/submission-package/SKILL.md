---
name: submission-package
description: >
  ใช้ skill นี้เมื่อผู้ใช้ต้องการ "submission package", "เตรียมส่ง journal", "ICMJE forms",
  "cover letter", "title page", "highlights", "graphical abstract", "key points",
  "author contributions", "competing interests", "funding statement", "data sharing statement",
  "suggested reviewers", "preferred reviewers", "opposed reviewers", "trial registration check",
  "ethics statement", "consent statement", "reporting guideline checklist file", "CONSORT checklist file",
  "STROBE checklist file", "PRISMA checklist file", "manuscript formatting check", "word count check",
  "reference style", "Vancouver style", "AMA style", "submission cover sheet"
metadata:
  version: "1.0.0"
  author: "Chagkrit"
  target_tier: "Q1 high-impact-factor"
---

# Submission Package — Q1/Q2 Journal Ready
# ระบบรวบรวมไฟล์ submission ครบชุดมาตรฐาน Q1/Q2

## บทบาท
ทำหน้าที่เป็น **Submission Manager** — รวบรวมเอกสารและตรวจสอบ formatting ให้พร้อมส่ง Q1/Q2 high-IF journal โดยปฏิบัติตาม **ICMJE Recommendations** + reporting guidelines + journal-specific instructions

---

## Standard Q1 Submission Inventory

| # | File | Required by | Format |
|---|---|---|---|
| 1 | Cover letter | All | docx/pdf, ≤1 page |
| 2 | Title page (separate, with COI) | Most Q1 | docx |
| 3 | Main manuscript (anonymised) | All | docx, structured |
| 4 | Abstract (often embedded) | All | structured, ≤300 w |
| 5 | Highlights / Key points | Many Q1 | 3–5 bullets |
| 6 | Graphical abstract | Increasing Q1 | png/tiff, journal spec |
| 7 | Tables file (separate or in manuscript) | All | docx/xlsx |
| 8 | Figures (individual) | All | tiff/png ≥300 DPI |
| 9 | Figure legends | Most | docx |
| 10 | Supplementary materials | All Q1 | pdf/xlsx/docx |
| 11 | Reporting guideline checklist | All Q1 | pdf (filled) |
| 12 | ICMJE author forms (per author) | All Q1 | pdf |
| 13 | Ethics approval letter | Required for human/animal | pdf |
| 14 | Trial registration confirmation | RCT | pdf |
| 15 | Suggested reviewers list | Most | embedded form |
| 16 | Opposed reviewers (if any) | Most | embedded form |
| 17 | Data sharing statement | All ICMJE-aligned | embedded in manuscript |
| 18 | Funding statement | All | embedded |
| 19 | Competing interests | All | embedded + ICMJE form |
| 20 | Author contributions (CRediT) | All Q1 | embedded |

---

## Smart Router

| Trigger | Mode |
|---|---|
| "submission package" / "เตรียมส่ง" | **Mode 1 — Full Package Assembly** |
| "cover letter" | **Mode 2 — Cover Letter** (or delegate to journal-strategy Mode 4) |
| "title page" | **Mode 3 — Title Page** |
| "highlights" / "key points" | **Mode 4 — Highlights** |
| "graphical abstract" | **Mode 5 — Graphical Abstract Spec** |
| "suggested reviewers" | **Mode 6 — Reviewer Suggestions** |
| "formatting check" / "word count" | **Mode 7 — Formatting QC** |
| "reporting checklist" | **Mode 8 — Reporting Guideline Checklist Build** |
| "ICMJE" / "author forms" | **Mode 9 — ICMJE Form Builder** |

---

## Mode 1 — Full Package Assembly

### Intake
```
1. Target journal name + Plan A confirmed
2. Manuscript draft path
3. Reporting guideline used (CONSORT/STROBE/STARD/TRIPOD/PRISMA/CARE)
4. Author list (name, degrees, affiliation, ORCID, email, contributions)
5. Ethics approval ID + IRB name + date
6. Trial registration ID (clinicaltrials.gov / WHO / ChiCTR)
7. Funding (source, grant numbers, role)
8. COI per author
9. Data availability statement (specify level)
10. Word/figure/table limits (auto from journal-strategy output)
```

### Build steps
```
1. Cover letter → Mode 2
2. Title page (separate) → Mode 3
3. Main manuscript: anonymise (remove author/affiliation), apply journal style
4. Abstract format + word count check
5. Highlights → Mode 4 (if journal requires)
6. Graphical abstract spec → Mode 5
7. Tables: format per journal (in-text or separate file)
8. Figures: confirm ≥300 DPI, file format (TIFF/EPS/PNG), naming
9. Figure legends: separate file
10. Supplementary: combine into one pdf or per-file
11. Reporting checklist → Mode 8 (fill referencing line numbers)
12. ICMJE forms → Mode 9
13. Ethics/registration letters: copy to package
14. Reviewer suggestions → Mode 6
15. Final formatting QC → Mode 7
```

### Output structure
```
/submission_<journal>_<date>/
  01_cover_letter.docx
  02_title_page.docx
  03_manuscript_anonymised.docx
  04_highlights.docx (if required)
  05_graphical_abstract.tiff
  06_tables.docx (or tables_combined.docx)
  07_figures/
      figure1.tiff
      figure2.tiff
      ...
  08_figure_legends.docx
  09_supplementary.pdf
  10_reporting_checklist.pdf
  11_ICMJE_forms/
      ICMJE_<author1>.pdf
      ...
  12_ethics_approval.pdf
  13_trial_registration.pdf
  14_suggested_reviewers.docx
  README.txt (file inventory + checklist)
```

---

## Mode 2 — Cover Letter
(delegated to `journal-strategy` Mode 4 — same template)

---

## Mode 3 — Title Page (separate file, Q1 standard)

```
TITLE
[Full title — ≤150 chars; specifies design and population]

SHORT TITLE (running head)
[≤50 chars]

AUTHORS
1. Author One, MD, MPH^1,2*    [ORCID: 0000-0000-0000-0000]
2. Author Two, PhD^3
3. ...
* Corresponding author

AFFILIATIONS
1. [Department, Institution, City, Country]
2. ...

CORRESPONDING AUTHOR
Name: ...
Address: ...
Email: ...
Phone: ...
ORCID: ...

WORD COUNT
Abstract:  XXX words
Main text: XXXX words (Intro/Methods/Results/Discussion)
Number of tables: X
Number of figures: X
Number of supplementary items: X

KEY WORDS
[5–8 MeSH terms]

FUNDING
[Source and grant numbers; role of funder]

CONFLICTS OF INTEREST
[Per author or "none declared"]

AUTHOR CONTRIBUTIONS (CRediT)
- Conceptualisation: ...
- Methodology: ...
- Software: ...
- Validation: ...
- Formal analysis: ...
- Investigation: ...
- Resources: ...
- Data curation: ...
- Writing — original draft: ...
- Writing — review & editing: ...
- Visualisation: ...
- Supervision: ...
- Project administration: ...
- Funding acquisition: ...

ETHICS APPROVAL
Approved by [IRB name], reference [number], date [yyyy-mm-dd].
Informed consent obtained from all participants.

TRIAL REGISTRATION (if RCT)
ClinicalTrials.gov NCT[number] (registered [date]).

DATA AVAILABILITY
[ICMJE-aligned statement — level: full / on request / by application / not available + reason]

DECLARATIONS
- All authors meet ICMJE criteria.
- No part has been published or is under consideration elsewhere.
- Manuscript prepared in accordance with [CONSORT/STROBE/STARD/TRIPOD] guideline.
```

---

## Mode 4 — Highlights / Key Points

Format ตาม journal:
- **Elsevier-style highlights**: 3–5 bullets, ≤85 chars each
- **JAMA key points**: Question / Findings / Meaning (~50–75 words each)
- **Lancet research in context**: Evidence before / Added value / Implications

Auto-draft 3 ตัวเลือกให้เลือก

---

## Mode 5 — Graphical Abstract Spec

```
Standard journal specs:
- Size: typically 12 × 6.5 cm or 560 × 1100 px
- Format: TIFF, EPS, or PDF (preferred), or high-res PNG
- Resolution: ≥300 DPI for raster
- Text: ≥7pt at final size
- Color: color-blind safe palette
- No copyrighted icons (use Servier Medical Art with attribution
  OR BioRender with license OR original)
```

Skill วาง outline 4-panel: 1) Setting/Population, 2) Methods, 3) Results, 4) Implication

---

## Mode 6 — Suggested Reviewers (Q1 standard)

**Selection criteria** (mandatory):
- Expert in topic/methodology
- Published ≥1 relevant paper in last 5 years
- **NOT** at same institution as any author
- **NOT** co-authored with any author in last 5 years
- **NOT** in same department even if different institution
- Diverse geographic representation (different countries preferred)
- ≥3 (typical), up to 5–6
- Include valid institutional email + ORCID (preferred)

ใช้ MCP `pubmed` ค้น senior authors / corresponding authors ของ key references

Output:
```
| # | Name | Affiliation | Country | Expertise | Email | ORCID | Justification |
|---|------|-------------|---------|-----------|-------|-------|--------------|
| 1 | ...  | ...         | UK      | ...       | ...   | ...   | "Recent senior author of [J Clin Invest 2024] addressing similar mechanism" |
```

**Opposed reviewers** (limit ≤2; require justification):
- Direct competitor with conflicting findings
- Prior contentious interactions documented

---

## Mode 7 — Formatting QC

Auto-checks:
```
□ Word count (abstract, main text, summary)
□ Reference count + style (Vancouver / AMA / APA per journal)
□ Reference format consistency
□ Tables ≤ journal max
□ Figures ≤ journal max
□ Color usage allowed?
□ Units SI / consistent
□ Abbreviations defined at first use; abbreviation list if journal requires
□ Statistical reporting per SAMPL (no "NS", no "p=0.000")
□ p-values to 3 decimals; <0.001 if smaller
□ Effect size + 95% CI present
□ Line numbering + double spacing (most journals)
□ Page numbers in footer
□ Anonymisation: no author names/affiliations/COI in main MS
□ No tracked changes / no comments
□ Figures embedded vs separate (per journal)
□ Supplementary referenced in text (S1, S2, ...)
□ All abbreviations in tables defined in footnotes
□ All p-values have test specified
```

Output: PASS / WARN / FAIL with fixes

---

## Mode 8 — Reporting Guideline Checklist Build

Auto-fill checklist (line-number referenced):
- CONSORT 2010 (RCT)
- STROBE 2007 (observational)
- STARD 2015 (diagnostic)
- TRIPOD 2015 (prediction)
- PRISMA 2020 (review)
- CARE (case report)
- SRQR / COREQ (qualitative)
- ARRIVE 2.0 (animal)

Format: official PDF template filled with manuscript line numbers

---

## Mode 9 — ICMJE Author Form Builder

ICMJE Disclosure Form (current version):
- Personal info
- COI types (financial: grants, consulting, royalties, stock; non-financial; etc.)
- Funding for current work
- Other relationships
- Date and signature

Skill prepares one form per author (template); user uploads each signed PDF

---

## Final Pre-Submission Checklist (Q1 standard)

```
═══════════════════════════════════════════════════════════════
PRE-SUBMISSION CHECKLIST — Q1/Q2 READY?
═══════════════════════════════════════════════════════════════
[ ] Cover letter ≤1 page, includes recent journal articles cited
[ ] Title page with all ICMJE-aligned declarations
[ ] Manuscript anonymised (no author/affil/COI)
[ ] Word count within journal limit
[ ] Abstract structured per journal style
[ ] Highlights / key points present (if required)
[ ] Graphical abstract per spec
[ ] Tables ≤ limit; all abbreviations defined
[ ] Figures ≥300 DPI; label-QC passed
[ ] Figure legends complete
[ ] Supplementary materials linked in text
[ ] Reporting guideline checklist filled (line numbers)
[ ] ICMJE forms signed by every author
[ ] Ethics approval letter attached
[ ] Trial registration confirmation (if RCT)
[ ] Suggested reviewers (≥3 with justifications)
[ ] Opposed reviewers (if any, with justification)
[ ] Data availability statement (ICMJE-aligned)
[ ] Funding statement complete
[ ] COI per author complete
[ ] Author contributions (CRediT) complete
[ ] References Vancouver/AMA per journal; all reachable
[ ] No prior submission overlap
[ ] All co-authors approved final version
═══════════════════════════════════════════════════════════════
   Status: ✅ Ready to upload  |  ❌ X items pending
═══════════════════════════════════════════════════════════════
```

---

## หลักการสำคัญ

- **อย่า** ส่งโดยไม่ตรวจ checklist ของ journal
- **อย่า** ใส่ชื่อผู้แต่งใน main MS (anonymisation)
- **อย่า** suggest reviewer ที่เป็น collaborator
- **ต้อง** มี ICMJE form ต่อทุก co-author
- **ต้อง** trial registration ก่อน first patient (RCT prospective)
- **ต้อง** ethics approval ก่อน data collection
- **ต้อง** reproducibility documents (audit trail, code, data dictionary)

---

## Companion Skills

- `journal-strategy` — Plan A/B/C + cover letter
- `manuscript-writer` — main draft
- `peer-review` — pre-submission QC
- `response-to-reviewers` — after decision
