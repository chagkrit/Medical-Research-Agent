# Medical Research Pipeline — Master Plugin

**End-to-end bilingual (Thai/English) pipeline** สำหรับนักวิจัยทางการแพทย์ที่ต้องการตีพิมพ์ใน
**Q1/Q2 journal ที่มี high impact factor** — ตั้งแต่เตรียมข้อมูลไปจนถึง response-to-reviewers

> Version 1.0.0 · Default target tier: **Q1 high-impact** (Lancet / NEJM / JAMA / BMJ / Nature Medicine tier)
> รองรับ Plan A/B/C strategy เมื่อไม่สามารถ target top tier ได้

---

## Claude Code Marketplace Installation

```bash
claude plugin marketplace add chagkrit/Medical-Research-Agent
claude plugin install medical-research-pipeline@medical-research-agent
```

After installation, restart Claude Code so the plugin skills and MCP server configuration are loaded.

---

## Pipeline Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│                     MASTER ORCHESTRATOR                              │
│            (master-orchestrator skill — entry point)                │
└──────────────────────────────────┬──────────────────────────────────┘
                                   │
        ┌──────────────────────────┼──────────────────────────┐
        ▼                          ▼                          ▼
┌───────────────┐         ┌───────────────┐         ┌───────────────┐
│  data-prep    │ ──────▶ │ medical-stats │ ──────▶ │  manuscript-  │
│ (clean+impute)│         │ (publish-ready│         │    writer     │
│               │         │  tables/figs) │         │  (IMRAD draft)│
└───────────────┘         └───────────────┘         └───────┬───────┘
                                                            │
                          ┌─────────────────────────────────┤
                          ▼                                 ▼
                  ┌───────────────┐                 ┌───────────────┐
                  │  peer-review  │                 │   journal-    │
                  │ (12 criteria) │                 │   strategy    │
                  └───────┬───────┘                 │ (Plan A/B/C)  │
                          │                         └───────┬───────┘
                          ▼                                 ▼
                  ┌───────────────────────────────────────────────┐
                  │            submission-package                  │
                  │  (cover letter · title page · ICMJE forms ·   │
                  │   highlights · suggested reviewers)           │
                  └───────────────────────┬───────────────────────┘
                                          ▼
                          ┌───────────────────────────────┐
                          │   response-to-reviewers        │
                          │   (rebuttal · point-by-point) │
                          └───────────────────────────────┘
```

---

## Skills in this Plugin

| # | Skill | บทบาท | Trigger (TH / EN) |
|---|-------|-------|-------------------|
| 1 | **master-orchestrator** | Entry point — route ระหว่างทุก skill | "เริ่มงานวิจัย", "publish in Q1", "start manuscript pipeline" |
| 2 | **data-prep** | Clean data, missing pattern, MICE imputation พร้อม CONSORT/STROBE-compliant audit trail | "เตรียมข้อมูล", "clean data", upload Excel |
| 3 | **medical-stats** | สถิติครบ Track A–F (RCT, Cohort, Case-Control, Survival, Diagnostic, Group Balancing) — publication-grade tables/figures (STATA-style ≥300 DPI) | "วิเคราะห์สถิติ", "Table 1", "Kaplan-Meier", "Cox", "ROC", "PSM" |
| 4 | **manuscript-writer** | เขียน IMRAD draft (EN output) ตาม CONSORT/STROBE/STARD/TRIPOD/PRISMA/CARE + Q1/Q2 citation search + consistency QC + deep review | "เขียน manuscript", "write paper", "IMRAD" |
| 5 | **peer-review** | Reviewer-grade peer review: 12-criteria + Reviewer Anticipation Matrix + Revision Planner | "peer review", "ตรวจ manuscript", "reviewer mode" |
| 6 | **journal-strategy** | เลือก journal Q1/Q2 high-IF — scope-fit matrix, IF/JCR lookup, Plan A/B/C, cover letter draft | "เลือก journal", "select journal", "Plan A B C" |
| 7 | **submission-package** | Assemble submission package — cover letter, title page, highlights, declarations, ICMJE forms, suggested reviewers, formatting check | "submission package", "เตรียมส่ง journal", "cover letter" |
| 8 | **response-to-reviewers** | Rebuttal letter, point-by-point response, revision tracking, resubmission strategy | "response to reviewers", "rebuttal", "ตอบ reviewer" |

> ทุก skill รองรับ Thai instructions / English manuscript output เพื่อ submission ระดับ international

---

## Default Standards (Q1 High-IF Mode)

| Aspect | Standard |
|--------|---------|
| Reporting guideline | CONSORT 2010 (RCT) · STROBE 2007 (observational) · STARD 2015 (diagnostic) · TRIPOD 2015 (prediction) · PRISMA 2020 (review) · CARE (case report) — **strict enforcement** |
| Statistical reporting | exact p-values to 3 decimals · 95% CI for every effect estimate · effect size always reported · sensitivity analyses required |
| References | ≥80% Q1/Q2 · cross-validated via PubMed + Consensus + Scholar Gateway · journal quartile flagged in every citation |
| Figures | STATA-style, ≥300 DPI, TIFF/PNG, label-overlap QC auto-run |
| Ethics / ICMJE | Ethics statement · trial registration · data sharing · author contributions · COI · funding — **mandatory in every submission** |
| Manuscript language | Native-grade academic English (refined via manuscript-master-workflow Mode C) |

---

## Typical End-to-End Workflows

### A. RCT → Lancet / NEJM tier
```
1. "เริ่มงานวิจัย RCT" → master-orchestrator
2. upload Excel → data-prep (CONSORT flow diagram, ITT/PP dataset)
3. medical-stats Track A → Table 1 + Primary/Secondary + Subgroup + Forest
4. manuscript-writer Mode 1 → CONSORT-compliant IMRAD draft (English)
5. peer-review → 12-criteria deep review
6. journal-strategy → Plan A (Lancet) / B (BMJ) / C (BMC Med)
7. submission-package → cover letter + ICMJE forms + title page
8. หลังได้ reviewer comments → response-to-reviewers
```

### B. Observational Cohort → JAMA Network / Annals tier
```
data-prep → medical-stats Track B (incidence, HR) + Track F (PSM if needed)
   → manuscript-writer STROBE → peer-review → journal-strategy → submission
```

### C. Diagnostic Accuracy → Radiology / Clinical Chemistry tier
```
data-prep → medical-stats Track E (ROC, DeLong, calibration, DCA)
   → manuscript-writer STARD → peer-review → journal-strategy
```

### D. Prognostic / Prediction Model → JAMA / Eur Heart J tier
```
data-prep → medical-stats (logistic + calibration + DCA + bootstrap validation)
   → manuscript-writer TRIPOD → peer-review → journal-strategy
```

---

## MCP Connectors (auto-loaded)

| MCP | Use |
|-----|-----|
| **pubmed** | Primary citation search — PMID, MeSH, abstracts |
| **consensus** | Verify scientific consensus for every claim |
| **scholar-gateway** | Landmark/highly-cited papers, missing-from-PubMed |

---

## Python Packages (auto-install)

```bash
pip install pandas numpy scikit-learn missingno openpyxl xlsxwriter \
            matplotlib seaborn scipy statsmodels pingouin lifelines \
            scikit-posthocs adjustText tableone causalml \
            python-docx --break-system-packages
```

---

## Companion Skill

Plugin นี้ทำงานร่วมกับ **`manuscript-master-workflow`** skill (ถ้าติดตั้งไว้) สำหรับ:
- Mode A: Manuscript Development (deep rewriting)
- Mode B: Peer Review (7 หมวด)
- Mode C: Academic English refinement
- Mode D: Journal selection deep-dive

หาก `manuscript-master-workflow` ไม่ได้ติดตั้ง — skills ในนี้สามารถทำงานครบวงจรได้ stand-alone

---

## Author

Chagkrit · Medical Research Pipeline v1.0.0 · Target: **Q1/Q2 high-impact-factor publication**
