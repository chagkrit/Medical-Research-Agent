---
name: response-to-reviewers
description: >
  ใช้ skill นี้เมื่อผู้ใช้ต้องการ "response to reviewers", "rebuttal letter", "ตอบ reviewer",
  "point-by-point response", "revision letter", "revise and resubmit", "R&R", "R1 revision",
  "R2 revision", "major revision response", "minor revision response", "appeal letter",
  "reviewer comments response", "editor response", "track changes", "marked-up manuscript",
  "summary of changes", "rebuttal strategy", "diplomatic response", "polite disagreement"
metadata:
  version: "1.0.0"
  author: "Chagkrit"
  target_tier: "Q1 high-impact-factor"
---

# Response to Reviewers — Rebuttal & Resubmission
# ระบบเขียน Response Letter ระดับ Q1/Q2

## บทบาท
ทำหน้าที่เป็น **Senior Author** ที่เขียน response letter แบบมืออาชีพ:
- **ขอบคุณ**, **ตรงประเด็น**, **อ้างหลักฐาน**, **ไม่แก้ตัว**
- ระบุการเปลี่ยนแปลง + อ้าง **line numbers** ของ revised manuscript
- รักษา tone ที่ดี **แม้เห็นต่าง** กับ reviewer

ผลลัพธ์ที่คาดหวัง: ลดจำนวน revision rounds, เพิ่มโอกาส acceptance

---

## Smart Router

| Trigger | Mode |
|---|---|
| ครั้งแรกหลัง decision letter | **Mode 1 — Triage & Strategy** |
| "เขียน response letter" | **Mode 2 — Point-by-Point Drafting** |
| "ขัดแย้งกับ reviewer" | **Mode 3 — Diplomatic Disagreement** |
| "summary of changes" | **Mode 4 — Summary for Editor** |
| "appeal" / "อุทธรณ์" | **Mode 5 — Appeal Letter** |
| "track changes" / "marked-up MS" | **Mode 6 — Marked-Up MS Preparation** |
| R2 / R3 round | **Mode 7 — Subsequent Revision Round** |

---

## Mode 1 — Triage & Strategy

### Read the decision letter

```
Identify:
  - Decision type:
      🔴 Reject (no resubmission)
      🟡 Reject with encouragement to resubmit as new
      🟢 Major revision (R&R)
      🟢 Minor revision
      ✅ Accept with revisions
      ✅ Accept
  - Reviewer count + their stance (positive/mixed/negative)
  - Editor's specific instructions
  - Deadline for revision
  - Word/figure additions allowed?
```

### Comment triage

แบ่งทุก comment เป็น:
| Tier | Definition | Required action |
|---|---|---|
| **Critical** | Affects validity/conclusions | Must address; may require new analysis |
| **Important** | Strengthens but not invalidating | Address with explanation/new content |
| **Minor** | Editorial / clarity / typos | Fix and acknowledge |
| **Optional** | Reviewer preference | Acknowledge; comply if reasonable |

### Strategy table (output)

```
| # | Reviewer | Comment summary | Tier | Action plan | Effort | Owner |
|---|----------|-----------------|------|-------------|--------|-------|
| 1 | R1 #1    | Add calibration | Crit | Re-run; add fig | 4h | Stats |
| 2 | R1 #2    | Multiplicity    | Crit | Apply Hochberg; rerun | 6h | Stats |
| 3 | R2 #1    | Define X        | Min  | Add definition  | 10m | Lead |
| ... |
```

---

## Mode 2 — Point-by-Point Drafting (Q1 standard)

### Response letter — top section

```
[Date]

Dear Editor [Surname],

Thank you for the opportunity to revise our manuscript
"[TITLE]" (Manuscript ID: [ID]). We are grateful to you and the
reviewers for the thoughtful and constructive comments, which
have substantially improved the manuscript.

Below we provide a point-by-point response to each comment.
For clarity, reviewer comments are shown in italics and our
responses follow in plain text; changes in the manuscript are
referenced by line numbers in the revised version (R1).

A marked-up manuscript with track changes is provided alongside
the clean version.

Sincerely,
[Corresponding author]
```

### Per-comment template (Q1 standard)

```
REVIEWER 1, COMMENT 1
─────────────────────────────────────────────────────────────
ITALIC: "[Verbatim reviewer comment, preserving wording]"

Response:
We thank the reviewer for this important observation. We agree
that [restating the point shows understanding]. To address this,
we have [specific action]:

  1. [What changed — specific]
  2. [Where it appears — line numbers, e.g., L142–158, Methods §2.4]
  3. [If new analysis — describe rationale + method + result]

Quoted revision (revised manuscript, L142–158):
   "..."

We have also updated [related section] to maintain consistency
(L210–215) and added Supplementary Figure S3 showing [analysis].

We hope this fully addresses the reviewer's concern.
─────────────────────────────────────────────────────────────
```

### Response patterns (Q1-grade phrasing)

| Situation | Recommended phrasing |
|---|---|
| Agree + change made | "We thank the reviewer for this important point. We have [action] (L###)." |
| Agree but partial change | "We agree in principle. We have [partial action], although a full [X] is beyond the current dataset's scope; we have acknowledged this in the limitations (L###)." |
| Disagree (politely) | "We appreciate the reviewer's perspective. After careful consideration, we have retained the original analysis because [evidence/reference]. We have clarified our rationale in the revised Methods (L###)." |
| Need clarification | "We thank the reviewer; we want to ensure we have understood correctly. The reviewer may be asking [interpretation A] or [interpretation B]. We have addressed both possibilities below…" |
| Cannot address (data limitation) | "We agree this would be valuable. Unfortunately, [reason]. We have explicitly added this as a limitation (L###) and have noted it as a priority for future work (L###)." |
| Acknowledge minor (typo / clarity) | "Corrected (L###). Thank you." |

### Required components per response
```
✅ Acknowledgement (1 short phrase)
✅ Restate the concern (shows understanding)
✅ Action taken (specific; cite line numbers)
✅ Quoted revision (when text changed)
✅ Reference to new analysis or figure if applicable
✅ If disagreement — provide evidence/citation
```

---

## Mode 3 — Diplomatic Disagreement

เมื่อ reviewer ผิด หรือ ขัดแย้ง:
1. **อย่า** ใช้ "the reviewer is mistaken" / "this is incorrect"
2. **ใช้** "We appreciate the reviewer's suggestion. We carefully considered this approach but [reason] [reference]."
3. ระบุ **evidence** ที่ support จุดยืน (cite literature, methodological standard, statistical principle)
4. หากเหมาะสม เสนอ **compromise** (sensitivity analysis ใช้วิธีของ reviewer)
5. ขอ editor ตัดสิน (ถ้า reviewers สอง คนขัดแย้งกัน) อย่างสุภาพ

ตัวอย่าง:
```
"We appreciate the reviewer's suggestion to use [Method X]. We
considered this approach but elected [Method Y] for three
reasons: (i) [reason with citation], (ii) [reason], and (iii)
[reason]. To address the reviewer's concern, we performed a
sensitivity analysis using [Method X], which is presented in
Supplementary Table S4. The results were directionally consistent
with the primary analysis (estimate X.XX vs Y.YY), supporting
the robustness of our conclusions. We have added a brief
discussion of this comparison in the revised manuscript (L###)."
```

---

## Mode 4 — Summary for Editor

ก่อน point-by-point ให้ editor เห็น **executive summary**:

```
SUMMARY OF MAJOR CHANGES
────────────────────────────────────────────────────────────
1. Added calibration analysis (new Figure 3, Methods §2.6,
   Results §3.3, L###–###).
2. Applied Hochberg multiplicity adjustment to secondary
   outcomes (Methods §2.5, Table 2).
3. Re-ran sensitivity analysis using complete-case data
   (Supplementary Table S5).
4. Strengthened limitations to address selection bias
   (Discussion ¶4, L###–###).
5. Updated 8 references: replaced 4 lower-quartile papers with
   Q1 sources.
6. Clarified definitions of [variable X] and [variable Y]
   throughout.
7. Corrected typographical and formatting errors.
────────────────────────────────────────────────────────────
Word count: prior 3,200 → revised 3,420 (within journal limit)
Tables: 4 (unchanged) | Figures: 3 → 4 | Supplementary: 5 → 7
Reporting checklist: CONSORT compliance 22/25 → 25/25
```

---

## Mode 5 — Appeal Letter (use sparingly)

เฉพาะเมื่อมีหลักฐานชัดว่า decision based on factual error:

```
Structure:
  ¶1 Express respect for the editorial process
  ¶2 Specific factual error in decision letter or review (quote)
  ¶3 Evidence correcting the error (data, reference)
  ¶4 Request: re-evaluation (not change of decision)
  ¶5 Offer to provide additional materials

Length: ≤1 page. No emotion. No accusations.
```

> ⚠️ ส่ง appeal เฉพาะกรณี factual misunderstanding — ไม่ใช่เพราะไม่พอใจ decision

---

## Mode 6 — Marked-Up Manuscript Preparation

```
Output files (always pair):
  1. revised_manuscript_R1_clean.docx     — no track changes, final version
  2. revised_manuscript_R1_tracked.docx   — all changes visible
  3. revised_manuscript_R1_highlighted.pdf — yellow highlights on changed text
```

Q1 best practice: include `line numbers` (continuous) in both files

---

## Mode 7 — Subsequent Revision Round (R2, R3, …)

```
Quote your previous response when relevant:
  "In response to Reviewer 1, Comment 3 (R1), we [action]. In R1,
   Reviewer 1 noted [follow-up concern], which we now address by
   [further action] (L###)."

Avoid re-litigating R1 issues unless reviewer revisits them.

Track responses in a master table:
| Round | Comment | Initial response | Reviewer follow-up | Final response |
```

---

## Output Files

1. `response_letter_R<n>_<date>.docx` — full point-by-point response
2. `editor_summary_R<n>_<date>.docx` — top-level summary
3. `revision_strategy_R<n>_<date>.xlsx` — triage table
4. `revised_manuscript_R<n>_clean.docx`
5. `revised_manuscript_R<n>_tracked.docx`

---

## หลักการสำคัญ

- **ขอบคุณก่อนเสมอ** (เริ่มทุก response ด้วย acknowledgement)
- **ระบุ line numbers** ของ revised MS ในทุก response
- **อย่า** เถียง — ใช้ evidence + diplomacy
- **อย่า** ทำ change โดยไม่ระบุใน response
- **อย่า** เพิกเฉยต่อ comment ใด — ตอบทุกข้อ
- **ต้อง** จัดส่ง clean + tracked versions
- **ต้อง** update reporting guideline checklist (line numbers)
- **ต้อง** ระบุการ revise ใหญ่ใน editor summary

---

## Companion Skills

- `manuscript-writer` — แก้เนื้อหา
- `medical-stats` — หาก reviewer ขอ analysis ใหม่
- `peer-review` — internal QC ก่อน resubmit
- `submission-package` — re-format submission files
