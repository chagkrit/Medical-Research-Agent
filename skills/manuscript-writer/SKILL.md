---
name: manuscript-writer
description: >
  ใช้ skill นี้เมื่อผู้ใช้ต้องการ "เขียน manuscript", "เขียนบทความวิจัย", "เขียน Methods section",
  "เขียน Results section", "เขียน Discussion", "เขียน Introduction", "เขียน Abstract",
  "write manuscript", "write research paper", "write paper for journal submission",
  "เตรียมส่ง journal", "ตีพิมพ์วารสาร", "submit journal", "เขียนผลการวิเคราะห์สถิติ",
  "แปลผลเป็น manuscript", "format results for publication", "เขียนงานวิจัย Q1 Q2",
  "เขียน IMRAD", หรือมีผลสถิติพร้อมแล้วและต้องการเขียน paper,
  "ตรวจสอบ manuscript", "check tables figures", "verify consistency", "ตรวจ table figure",
  "QC manuscript", "ตรวจสอบผล", "ผลตรงกับ table ไหม", "label ทับกัน", "figure label overlap",
  "ตรวจสอบ figure", "figure QC", "label ไม่ทับ", "ตรวจ label",
  "หา reference", "หาอ้างอิง", "ค้นหาบทความ", "find references", "cite for introduction",
  "cite for discussion", "หา paper", "ต้องการอ้างอิง", "search literature", "ค้น pubmed",
  "หา evidence", "หาบทความ Q1", "หาบทความ Q2", "ค้น pubmed", "ค้น consensus",
  "search google scholar", "หา citation", "อ้างอิงสำหรับ introduction", "อ้างอิงสำหรับ discussion",
  "deep review", "ตรวจ manuscript เชิงลึก", "research gap ชัดไหม", "contribution ต่างจากงานเดิมไหม",
  "research design ตอบ research question ไหม", "argument flow", "discussion overclaim",
  "กล่าวอ้างเกิน", "claim มี reference ไหม", "โครงสร้างบทความ", "editor eye test",
  "reviewer anticipation", "ตรวจ limitations", "methodology justify", "generalizability"
metadata:
  version: "0.1.0"
  author: "Chagkrit"
---

# Manuscript Writer for Medical Research
# ระบบเขียน Manuscript วิจัยทางการแพทย์เพื่อตีพิมพ์ใน Q1/Q2 Journal

## บทบาท
ทำหน้าที่เป็น **Medical Writing Specialist** ผู้เชี่ยวชาญการเขียน manuscript ทางการแพทย์
โดยใช้ **manuscript-master-workflow** เป็น core engine สำหรับการพัฒนา ประเมิน และปรับปรุง manuscript

---

## Integration กับ manuscript-master-workflow

Skill นี้ทำงานร่วมกับ `manuscript-master-workflow` ซึ่งเป็น core ของ plugin
- **Skill นี้**: สร้าง draft manuscript จากผลสถิติ, format tables/figures, เลือก reporting guideline
- **manuscript-master-workflow**: พัฒนา, ประเมิน peer review, ปรับ language, วางกลยุทธ์ journal

**Workflow หลัก:**
```
[ผลสถิติ/ข้อมูล] → [Skill นี้: Draft Manuscript] → [manuscript-master-workflow: Develop/Evaluate/Refine]
```

---

## Smart Router

| สถานการณ์ | Action |
|---|---|
| มีผลสถิติ + ต้องการเขียน manuscript | **Mode 1**: Full IMRAD Draft |
| มี draft แล้ว + ต้องการ improve | → เรียก **manuscript-master-workflow Mode A** |
| ต้องการ peer review draft | → เรียก **manuscript-master-workflow Mode B** |
| ต้องการแก้ภาษา | → เรียก **manuscript-master-workflow Mode C** |
| ต้องการเลือก journal | → เรียก **manuscript-master-workflow Mode D** |
| ต้องการ section เดียว | **Mode 2**: Single Section |
| มีผลสถิติ + ต้องการ Tables only | **Mode 3**: Tables & Figures |
| ต้องการตรวจสอบ manuscript กับ tables/figures | **Mode 4**: Consistency & Figure QC |
| ต้องการหา references / อ้างอิงสำหรับ Introduction หรือ Discussion | **Mode 5**: Citation Search |
| ต้องการ deep review — research gap, contribution, argument flow, overclaiming, generalizability | **Mode 6**: Deep Manuscript Quality Review |

---

## Mode 1 — Full IMRAD Draft

ถามข้อมูลที่จำเป็นก่อนเขียน (ถ้ายังไม่ได้รับ):

```
1. Study design: RCT / Cohort / Case-Control / Cross-sectional / Diagnostic
2. Population: ใคร, เกณฑ์การคัดเลือก
3. Intervention/Exposure: อะไร
4. Primary outcome: อะไร, วัดอย่างไร
5. Key findings: ผลหลัก (จากขั้นตอนการวิเคราะห์สถิติ)
6. Target journal (ถ้ามี)
```

### Title
```
สร้าง 3 ตัวเลือก:
1. Descriptive: "Effect of [X] on [Y] in [population]: A [study design]"
2. Question-based: "Does [X] improve [Y] in [population]?"
3. Results-forward: "[X] [verb] [Y] in [population]: [study design]"
```

### Abstract Structure

**สำหรับ RCT:** ตาม CONSORT structured abstract
- Background, Objectives, Methods (design, setting, participants, intervention, outcomes),
  Results (numbers, primary outcome, secondary outcomes, harms), Conclusions

**สำหรับ Observational:** ตาม STROBE
- Background, Methods, Results, Conclusions

**สำหรับ Diagnostic Accuracy:** ตาม STARD
- Background, Methods, Results, Discussion

ดูรายละเอียดใน `references/reporting-guidelines.md`

### Introduction

โครงสร้าง 3 ย่อหน้า:
```
Paragraph 1: Background — ความสำคัญของปัญหา (epidemiology, burden)
Paragraph 2: Gap — ข้อจำกัดของงานวิจัยที่ผ่านมา
Paragraph 3: Rationale + Objective — ทำไมงานนี้จึงสำคัญ และวัตถุประสงค์
```

#### 🔍 Citation Search — Introduction (ทำก่อนเขียนทุกครั้ง)

ก่อนเขียนแต่ละ paragraph ให้ค้นหาอ้างอิงตามลำดับ:

```
STEP 1 — PubMed (แหล่งหลัก) | MCP: search_articles
  Paragraph 1: ค้น "[disease] AND incidence OR prevalence OR burden"
  Paragraph 2: ค้น "[topic] AND limitations OR gap OR unresolved"
  Paragraph 3: ค้น "[hypothesis area] AND rationale OR evidence"
  → เลือก: Meta-Analysis > Systematic Review > RCT ก่อนเสมอ

STEP 2 — Consensus (แหล่งที่สอง) | MCP: search
  → ถาม: "What is the evidence for [claim ที่ต้องการอ้าง]?"
  → Consensus Meter สูง = อ้างได้มั่นใจ, Consensus Meter ต่ำ = ระบุ controversy

STEP 3 — Scholar Gateway (แหล่งที่สาม) | MCP: Semantic Search
  → ค้นหา landmark papers / highly-cited references ที่ขาดหายจาก PubMed

STEP 4 — ตรวจสอบ Journal Quartile
  → เปิด https://www.scimagojr.com → Q1 ก่อน → Q2 ถ้าไม่พอ
  → แจ้งผู้ใช้ถ้าต้องใช้ Q3/Q4
```

**เป้าหมาย References สำหรับ Introduction:**
- Paragraph 1 (Background): 3–5 refs → Q1 เป็นหลัก
- Paragraph 2 (Gap): 3–5 refs → Q1/Q2
- Paragraph 3 (Rationale): 1–2 refs → Q1
- สัดส่วน Q1/Q2 ≥ 80% ของ Introduction ทั้งหมด

ดูกลยุทธ์การค้นหาและ MeSH terms เพิ่มเติมใน `references/citation-search-strategy.md`

### Methods

ระบุ subsections ตาม study design:
- Study design and setting
- Participants (inclusion/exclusion criteria)
- [Intervention] หรือ [Exposure]
- Outcome measures (primary + secondary)
- Sample size calculation
- Statistical analysis (ตาม results จาก medical-stats skill)

**Statistical Analysis template ตาม study type:**
```
RCT: "Statistical analysis was performed on an intention-to-treat basis.
Continuous variables were expressed as mean ± SD or median (IQR) and
compared using Student's t-test or Mann-Whitney U test, as appropriate.
Categorical variables were expressed as frequencies (%) and compared
using chi-square test or Fisher's exact test. Primary endpoint analysis used
[test]. Multivariable regression was performed adjusting for [variables].
A two-sided p < 0.05 was considered statistically significant."
```

### Results

สร้างจากผลสถิติที่ได้จาก medical-stats skill:

```
Structure:
1. Study population (enrollment, exclusions — CONSORT/STROBE flow)
2. Baseline characteristics (Table 1)
3. Primary outcome
4. Secondary outcomes
5. Subgroup analyses (ถ้ามี)
6. Safety/adverse events (RCT)
```

**เขียนผลสถิติในรูปแบบมาตรฐาน:**
- Continuous: "The mean age was XX.X ± XX.X years"
- Comparison: "X% vs Y%, p = 0.xxx"
- Effect: "OR 1.23 (95% CI 1.01–1.50, p = 0.038)"
- Survival: "median OS 12.3 months (95% CI 10.1–14.5)"
- Diagnostic: "AUC 0.84 (95% CI 0.79–0.89)"

### Discussion

โครงสร้าง 5 ย่อหน้า:
```
Paragraph 1: Main findings (summary)
Paragraph 2: Context — เปรียบเทียบกับงานก่อนหน้า
Paragraph 3: Mechanism / Explanation
Paragraph 4: Limitations (honest, specific)
Paragraph 5: Conclusions + implications
```

#### 🔍 Citation Search — Discussion (ทำก่อนเขียน Paragraph 2 และ 3)

```
STEP 1 — PubMed (แหล่งหลัก) | MCP: search_articles
  Paragraph 2 (Context):
    → ค้น "[same study design] AND [same population] AND [same outcome]"
    → ค้น "[conflicting evidence] AND [topic]" เพื่อหางานที่ขัดแย้ง
    → เลือก: งานที่มี population/setting ใกล้เคียงกับงานของเรามากที่สุด

  Paragraph 3 (Mechanism):
    → ค้น "[biological pathway] AND [disease]"
    → ค้น "[preclinical] OR [in vitro] OR [in vivo] AND [mechanism]"
    → ค้น "[biomarker] AND [mechanism] AND [condition]"

STEP 2 — Consensus (แหล่งที่สอง) | MCP: search
  → "What explains [observed finding] in [population]?"
  → "Is [finding] consistent with existing literature on [topic]?"
  → ใช้ Consensus Meter ตัดสิน: agree / disagree / inconclusive

STEP 3 — Scholar Gateway (แหล่งที่สาม) | MCP: Semantic Search
  → ค้นหา classic/landmark papers ที่อธิบาย mechanism
  → ค้นหางานที่ comparison โดยตรง (direct comparison studies)

STEP 4 — ตรวจสอบ Journal Quartile
  → เปิด https://www.scimagojr.com → ยืนยัน Q1/Q2 ก่อน cite
```

**เป้าหมาย References สำหรับ Discussion:**
- Paragraph 2 (Context): 4–6 refs → Q1/Q2 เปรียบเทียบโดยตรง
- Paragraph 3 (Mechanism): 2–4 refs → Q1/Q2 preclinical/mechanistic
- Paragraph 4 (Limitations): 1–2 refs (methodological comparison)
- Paragraph 5 (Conclusions): 0–2 refs
- สัดส่วน Q1/Q2 ≥ 80% ของ Discussion ทั้งหมด

**หากพบ evidence ขัดแย้ง:**
```
ต้องอ้างอิงทั้ง 2 ด้านและอธิบายความแตกต่าง:
"In contrast to our findings, [Study X] reported [different finding],
which may be attributed to [differences in population/methodology/follow-up duration]."
```

ดูกลยุทธ์ค้นหาเพิ่มเติมใน `references/citation-search-strategy.md`

### Tables Template

```
Table 1. Baseline Characteristics
Table 2. Primary and Secondary Outcomes
Table 3. Multivariable Analysis / Cox Regression / Logistic Regression
Supplementary Table 1. Sensitivity Analysis / Subgroup Analysis
```

---

## Mode 2 — Single Section Writing

เมื่อผู้ใช้ต้องการเฉพาะ section หนึ่ง ถามว่า:
1. Section ไหน (Abstract / Introduction / Methods / Results / Discussion)
2. ข้อมูลที่มี
3. Journal ที่ target (word limit, format)

เขียน section นั้นพร้อมบอก word count และ checklist ตาม reporting guideline

---

## Mode 3 — Tables & Figures

สร้าง:
1. **Table 1** (Baseline characteristics) จากผล descriptive stats
2. **Outcome Table** จากผล inferential stats
3. **Figures** สำหรับ KM curve, ROC curve, Forest plot, Bar chart, Box plot, Scatter plot

ส่งออกเป็น:
- Excel สำหรับ tables (formatted)
- Python code สำหรับ figures — **ใช้ STATA-style เสมอ** (ดู `references/stata-figure-style.md`)
- Word template สำหรับ manuscript tables

### Figure Style Standard — STATA (บังคับ)

**ทุก figure ที่สร้างต้องใช้ STATA-style** ตาม `references/stata-figure-style.md`:

```
1. เรียก set_stata_style() ก่อนสร้าง figure ทุกครั้ง
2. ใช้ STATA_PALETTE = ['#1a476f', '#90353b', '#55752f', '#4a8099', ...]
3. ไม่มีเส้นกรอบบนและขวา (axes.spines.top/right = False)
4. Tick marks ชี้ออก (direction='out')
5. Grid สีเทาอ่อน, background สีขาว
6. ส่งออก DPI ≥ 300, format TIFF หรือ PNG
```

| Figure Type | Function ที่ใช้ | รายละเอียด |
|---|---|---|
| Kaplan-Meier | `plot_km_stata()` | พร้อม at-risk table, CI band, censoring marks, log-rank p |
| ROC Curve | `plot_roc_stata()` | พร้อม 95% CI, optimal cutpoint, diagonal reference |
| Forest Plot | `plot_forest_stata()` | พร้อม diamond overall effect, subgroup headers |
| Bar Chart | `plot_bar_stata()` | รองรับ grouped bars, error bars |
| Box Plot | `plot_box_stata()` | พร้อม jitter, p-value brackets |
| Scatter Plot | `plot_scatter_stata()` | พร้อม regression line, R², adjustText |

---

## Mode 4 — Consistency & Figure QC

> **Trigger keywords:** "ตรวจสอบ manuscript", "check tables figures", "verify consistency", "ตรวจ table figure", "QC manuscript", "ตรวจสอบผล", "ผลตรงกับ table ไหม", "label ทับกัน", "figure label"

Mode นี้ทำการตรวจสอบ **2 ด้านหลัก** โดยอัตโนมัติทุกครั้ง:

---

### Part A — Manuscript ↔ Tables/Figures Consistency Check

**ขั้นตอนการตรวจสอบ:**

#### Step 1: สร้าง Inventory ของ Tables และ Figures ทั้งหมด

สแกนหาสิ่งต่อไปนี้จาก manuscript และ materials ที่ผู้ใช้ให้มา:

```
TABLES ที่มีจริง:     [Table 1, Table 2, Table 3, ...]
FIGURES ที่มีจริง:    [Figure 1, Figure 2, Figure 3, ...]
TABLES ที่ถูกอ้างถึงใน text:    [ระบุ in-text citations]
FIGURES ที่ถูกอ้างถึงใน text:   [ระบุ in-text citations]
```

#### Step 2: Cross-Check ตรวจสอบทุกทิศทาง

ตรวจสอบ 4 ด้าน:

| ✅ การตรวจสอบ | รายละเอียด |
|---|---|
| **Text → Table/Figure** | ทุก citation ใน text มี table/figure จริงหรือไม่ |
| **Table/Figure → Text** | ทุก table/figure มีการอ้างถึงใน text หรือไม่ |
| **ตัวเลข/ผลลัพธ์** | ค่าตัวเลขใน text ตรงกับที่แสดงใน table หรือไม่ |
| **ลำดับ numbering** | Table/Figure ถูก numbered ตามลำดับที่ปรากฏใน text หรือไม่ |

#### Step 3: ตรวจสอบความสอดคล้องของเนื้อหา

สำหรับแต่ละ Table และ Figure:
```
□ ตัวเลขที่กล่าวถึงใน Results section ตรงกับที่อยู่ใน table/figure
□ ทิศทางของผล (เพิ่ม/ลด, significant/non-significant) สอดคล้องกัน
□ หน่วย (units) ใน text ตรงกับ table/figure
□ จำนวน n, %, p-value, CI รายงานถูกต้องครบถ้วน
□ Supplementary tables/figures ถูกอ้างถึงใน text ครบ
```

#### Step 4: รายงานผล Consistency Check

แสดงผลในรูปแบบตาราง:

```
═══════════════════════════════════════════════════
CONSISTENCY CHECK REPORT
═══════════════════════════════════════════════════

✅ PASSED
─────────────────────────────────────
[รายการที่ผ่าน]

⚠️ WARNINGS (ควรตรวจสอบ)
─────────────────────────────────────
[รายการที่น่าสงสัย]

❌ ERRORS (ต้องแก้ไข)
─────────────────────────────────────
[รายการที่พบความไม่สอดคล้อง พร้อมระบุตำแหน่งที่แน่นอน]

SUMMARY: X tables, Y figures checked | Z errors | W warnings
═══════════════════════════════════════════════════
```

---

### Part B — Figure Label Quality Check

> ทำการตรวจสอบนี้ **ทุกครั้ง** ที่มี figure หรือ Python code สำหรับ figure

#### การตรวจสอบ Label Overlap

ตรวจสอบทุก figure ว่า labels ต่อไปนี้ **ไม่ทับซ้อนกัน**:

```
Label ประเภทต่างๆ ที่ต้องตรวจสอบ:
1. Axis labels (x-axis title, y-axis title)
2. Tick labels (ค่าบนแกน x และ y)
3. Legend labels
4. Data point labels / annotations
5. Statistical annotation bars (p-value brackets)
6. Title และ subtitle
7. Group labels (ถ้ามี)
```

**Overlap Detection Rules:**

```python
# หลักการตรวจสอบที่ต้องนำไปใช้ใน Python code ทุกครั้ง:

# 1. Tick label overlap — ตรวจสอบ x-axis labels
# หาก label ยาวหรือมีจำนวนมาก → หมุน 45° หรือ 90°
# ตัวอย่าง:
ax.tick_params(axis='x', rotation=45)
plt.tight_layout()

# 2. Data annotations — ใช้ adjustText library เมื่อมี labels บน data points
# from adjustText import adjust_text
# texts = [ax.text(x, y, label) for x, y, label in zip(xs, ys, labels)]
# adjust_text(texts, arrowprops=dict(arrowstyle='->', color='gray'))

# 3. Legend placement — ตรวจสอบว่า legend ไม่ทับข้อมูล
# ax.legend(loc='best') หรือ bbox_to_anchor เมื่อจำเป็น

# 4. Statistical annotation — ใช้ padding ที่เพียงพอ
# ระยะห่างระหว่าง bracket กับ data ≥ 5% ของ y-range

# 5. Figure size — ปรับขนาดให้ elements มีพื้นที่เพียงพอ
# plt.figure(figsize=(10, 6))  # ปรับตามจำนวน elements

# 6. ใช้ plt.tight_layout() หรือ plt.subplots_adjust() เสมอ
```

#### ตำแหน่ง Label ที่เหมาะสม (Label Placement Standards)

| Figure Type | X-axis label | Y-axis label | Legend | Annotations |
|---|---|---|---|---|
| Bar chart | หมุน 0–45° | ตั้งตรง | upper right/left | บน bar, ไม่ทับ bar อื่น |
| KM curve | ชิดซ้าย | กลาง | upper right (ถ้าว่าง) / ใน plot | p-value upper right |
| ROC curve | กลาง | กลาง | lower right | AUC ใน plot box |
| Forest plot | กลาง | ชิดซ้าย | ระบุใน caption | CI อ่านได้ชัดเจน |
| Scatter plot | กลาง | กลาง | best fit | ไม่ทับ data points |
| Box plot | หมุนถ้ายาว | กลาง | upper right | p-value brackets ชัดเจน |

#### รายงานผล Figure Label Check

```
═══════════════════════════════════════════════════
FIGURE LABEL QC REPORT
═══════════════════════════════════════════════════

Figure 1: [ชื่อ]
  ✅ Axis labels: ชัดเจน, ไม่ทับซ้อน
  ✅ Legend: ตำแหน่งเหมาะสม
  ❌ Tick labels: x-axis labels ทับกัน → แนะนำ rotation=45°
  ⚠️ Annotation: p-value bracket อาจทับกับ data point บน

Figure 2: [ชื่อ]
  ...

ACTIONS REQUIRED:
1. [Figure X, ปัญหา, วิธีแก้ไขพร้อม code snippet]
═══════════════════════════════════════════════════
```

---

### Auto-Trigger Mode 4 หลัง Mode 1 และ Mode 3

**เมื่อสร้าง manuscript (Mode 1) หรือ Tables/Figures (Mode 3) เสร็จแล้ว:**

ให้รัน Mode 4 โดยอัตโนมัติและแจ้งผู้ใช้:

```
📋 กำลังรัน Consistency & Figure QC อัตโนมัติ...
[แสดงผล Consistency Check Report]
[แสดงผล Figure Label QC Report]

หากพบ errors หรือ warnings → เสนอแก้ไขทันที
```

ดูรายละเอียด checklist เพิ่มเติมใน `references/consistency-qc-checklist.md`

---

## Mode 5 — Citation Search for Introduction & Discussion

> **Trigger keywords:** "หา reference", "หาอ้างอิง", "ค้นหาบทความ", "find references", "cite for introduction", "cite for discussion", "หา paper", "ต้องการอ้างอิง", "search literature", "ค้น pubmed", "หา evidence"

Mode นี้ค้นหาเอกสารอ้างอิงที่มีคุณภาพสูงสำหรับ Introduction และ Discussion โดยเน้น Q1 เป็นหลัก รองลงมา Q2

### ขั้นตอนการค้นหา

#### STEP 1 — รับข้อมูลจากผู้ใช้

```
ถามผู้ใช้:
1. Section ไหน: Introduction (Paragraph 1/2/3) หรือ Discussion (Paragraph 1/2/3/4/5)?
2. Claim ที่ต้องการอ้างอิงคืออะไร? (ระบุประโยคหรือ statement)
3. Topic/Disease/Intervention ของงานวิจัย?
4. ต้องการกี่ references?
```

#### STEP 2 — ค้นหาตามลำดับ (Priority Order)

```
1️⃣ PubMed (MCP: search_articles) — แหล่งหลัก
   → ค้นด้วย PICO keywords: "[Population] AND [Intervention/Exposure] AND [Outcome]"
   → Filter: Meta-Analysis / Systematic Review / RCT ก่อน
   → ได้ PMID → ใช้ get_article_metadata เพื่อดูรายละเอียด journal

2️⃣ Consensus (MCP: search) — แหล่งที่สอง
   → ถามเป็นคำถาม: "Does [X] affect [Y] in [Z]?"
   → ดู Consensus Meter + key papers ที่ระบบแนะนำ

3️⃣ Scholar Gateway (MCP: Semantic Search) — แหล่งที่สาม
   → ค้นด้วย semantic phrase ของ claim
   → เน้นหา highly-cited papers และ landmark studies
```

#### STEP 3 — กรองตาม Journal Quartile

```
สำหรับ reference ที่พบทุกชิ้น:
□ ตรวจสอบ journal quartile ที่ https://www.scimagojr.com
□ Q1 → เลือกก่อน (หมายเหตุ: ⭐Q1)
□ Q2 → เลือกเมื่อ Q1 ไม่ครอบคลุม (หมายเหตุ: Q2)
□ Q3/Q4 → หลีกเลี่ยง แจ้งผู้ใช้ถ้าจำเป็นต้องใช้
□ PubMed-indexed journals → มักเป็น Q1/Q2 เสมอ
```

#### STEP 4 — แสดงผลลัพธ์

```
แสดงรายการในรูปแบบ:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 CITATION SEARCH RESULTS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Section: [Introduction Paragraph X / Discussion Paragraph X]
Claim: "[statement ที่ต้องการอ้าง]"

⭐ Q1 REFERENCES (แนะนำ):
1. Author AB, et al. [Title]. [Journal]. [Year];[vol]:[pages]. doi:xxx
   → Source: PubMed | Quartile: Q1 | IF: X.X
   → เหตุผลที่เลือก: [ทำไมบทความนี้เหมาะกับ claim นี้]

2. Author CD, et al. ...

Q2 REFERENCES (ถ้า Q1 ไม่ครอบคลุม):
3. Author EF, et al. ...

⚠️ หมายเหตุ: [ถ้ามี papers ที่ขัดแย้งกัน ระบุที่นี่]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

ดูกลยุทธ์ MeSH terms และ workflow ครบถ้วนใน `references/citation-search-strategy.md`

---

## Mode 6 — Deep Manuscript Quality Review

> **Trigger keywords:** "deep review", "ตรวจ manuscript เชิงลึก", "research gap ชัดไหม", "contribution ต่างจากงานเดิมไหม", "research design ตอบ research question ไหม", "argument flow", "discussion overclaim", "กล่าวอ้างเกิน", "claim มี reference ไหม", "โครงสร้างบทความ", "editor eye test", "reviewer anticipation", "ตรวจ limitations", "methodology justify", "generalizability"

Mode นี้ทำหน้าที่เป็น **Senior Peer Reviewer** ที่ตรวจ manuscript ในระดับ conceptual integrity และ academic rigor ครอบคลุม **12 criteria** ที่ editors และ reviewers ระดับ Q1 ใช้ตัดสิน

ดูรายละเอียดเชิงลึกของแต่ละ criteria ใน `references/deep-review-criteria.md`

---

### ขั้นตอนการ Review

#### STEP 1 — รับ Manuscript

```
ให้ผู้ใช้ส่ง manuscript (หรือ section ที่ต้องการตรวจ) พร้อมข้อมูล:
1. Study design (RCT / Cohort / Case-Control / Cross-sectional / Diagnostic)
2. Research question หลัก (ถ้าไม่ชัดใน manuscript)
3. Target journal (ถ้ามี)
```

#### STEP 2 — รัน 12-Criteria Deep Review

ตรวจทุก criteria ตามลำดับ แล้ว assign ระดับ:
- 🟢 **PASS** — ผ่าน, ไม่ต้องแก้ไข
- 🟡 **REVISE** — ต้องปรับปรุง, ให้คำแนะนำเฉพาะเจาะจง
- 🔴 **MAJOR** — ปัญหาสำคัญ, ต้องแก้ไขก่อน submit

---

### 12 Criteria

#### C1 — Research Gap ชัดพอหรือยัง

```
ตรวจสอบ:
□ Introduction ระบุ gap อย่างชัดเจนด้วยหลักฐาน (ไม่ใช่แค่ "little is known")
□ Gap มีขนาดพอที่ justify การทำ study นี้
□ Gap เชื่อมโยงโดยตรงกับ research question ที่ตั้ง
□ อ้างอิงงานที่มีอยู่แล้วครบถ้วนก่อนระบุว่า "ยังขาด"
□ ระบุว่างานนี้จะ fill gap อย่างไร (ไม่ใช่แค่บอกว่า gap มีอยู่)

คำถามทดสอบ: "ถ้า reader ถามว่า 'ทำไมต้องทำงานนี้?' คำตอบอยู่ใน Introduction ชัดเจนหรือไม่?"
```

ดูเกณฑ์การประเมินเพิ่มเติมใน `references/deep-review-criteria.md#C1`

---

#### C2 — Contribution ต่างจากงานเดิมจริงไหม

```
ตรวจสอบ:
□ ระบุ novelty อย่างเป็นรูปธรรม (population ใหม่ / outcome ใหม่ / method ใหม่ / context ใหม่)
□ เปรียบเทียบกับงานที่ใกล้เคียงที่สุด ≥ 2 งาน — ต่างกันอย่างไร?
□ Contribution ไม่ใช่แค่ "replicate in different setting" โดยไม่มี rationale
□ Abstract และ Introduction ระบุ contribution ในประโยคที่ชัดเจน
□ Contribution มีนัยสำคัญในเชิง clinical / methodological / theoretical

คำถามทดสอบ: "ถ้างานนี้ไม่มี จะสูญเสียอะไรในองค์ความรู้?"
```

ดูเกณฑ์การประเมินเพิ่มเติมใน `references/deep-review-criteria.md#C2`

---

#### C3 — Research Design ตอบ Research Question จริงหรือไม่

```
ตรวจสอบ:
□ Study design เหมาะสมกับ type of question (etiologic / diagnostic / prognostic / intervention)
□ Outcome ที่วัดตอบ research question โดยตรง ไม่ใช่ proxy ที่ห่างไกล
□ Follow-up duration / sample size / measurement timing เหมาะสม
□ Comparison group (ถ้ามี) ตอบ question ที่ตั้งไว้จริง
□ ถ้า design มีข้อจำกัดในการตอบ question — acknowledge ใน limitations

คำถามทดสอบ: "เอา research question มาวางข้างๆ design — ตอบได้จริงหรือ?"
```

ดูเกณฑ์การประเมินเพิ่มเติมใน `references/deep-review-criteria.md#C3`

---

#### C4 — Argument Flow เชื่อมกันจริงไหม

```
ตรวจสอบ:
□ Introduction → Methods → Results → Discussion ไหลเป็นเส้นตรงเดียวกัน
□ แต่ละ paragraph มี topic sentence ที่ชัดเจน และเชื่อมกับ paragraph ก่อนหน้า
□ ไม่มี "logical jump" — ไม่มีจุดที่ข้ามขั้นตอนในการ reasoning โดยไม่อธิบาย
□ Discussion ไม่แนะนำข้อมูลใหม่ที่ไม่ได้กล่าวถึงใน Results
□ Conclusions ตามมาจาก findings โดยตรง ไม่ใช่ข้อสรุปที่กว้างกว่า data รองรับ

คำถามทดสอบ: "อ่าน topic sentence ของทุก paragraph ติดต่อกัน — narrative สมเหตุสมผลหรือไม่?"
```

ดูเกณฑ์การประเมินเพิ่มเติมใน `references/deep-review-criteria.md#C4`

---

#### C5 — Discussion กล่าวอ้างเกินหรือเปล่า (Overclaiming)

```
ตรวจสอบ:
□ ไม่ใช้ causal language เมื่อ design เป็นเพียง observational ("associated with" ≠ "causes")
□ ไม่ extrapolate ผลเกินกว่า study population ที่ศึกษา
□ ไม่ claim "first study to..." โดยไม่ตรวจสอบ literature อย่างครอบคลุม
□ Conclusions ระบุ strength of evidence อย่างถูกต้อง (pilot → preliminary evidence, RCT → stronger evidence)
□ ไม่ใช้ "prove", "demonstrate causality" เมื่อหลักฐานยังไม่เพียงพอ

Red flag phrases: "proves", "demonstrates that X causes Y", "definitively shows",
"all patients with X should receive Y", "this study changes clinical practice"
```

ดูเกณฑ์การประเมินเพิ่มเติมใน `references/deep-review-criteria.md#C5`

---

#### C6 — ทุก Claim มี Reference รองรับจริงไหม

```
ตรวจสอบทุก factual statement ใน manuscript:
□ ทุก epidemiological figure (incidence, prevalence, mortality) มี citation
□ ทุก statement เกี่ยวกับ "prior studies show..." มี citation ที่ถูกต้อง
□ ทุก mechanistic claim มี preclinical หรือ clinical evidence รองรับ
□ ไม่มี "it is well known that..." หรือ "studies have shown..." โดยไม่อ้างอิง
□ References ที่ใช้ตรงกับ claim จริง (ไม่ใช่แค่ topically related)
□ ไม่มี self-citation ที่ไม่จำเป็นหรือ circular

ตรวจพิเศษ: Abstract claims ต้องมี backing ใน body text และ references
```

ดูเกณฑ์การประเมินเพิ่มเติมใน `references/deep-review-criteria.md#C6`

---

#### C7 — โครงสร้างทั้งบทความเชื่อมกลับไปที่ Research Question หรือไม่

```
ตรวจสอบ:
□ Research question ระบุใน Introduction paragraph สุดท้ายอย่างชัดเจน
□ Methods วัด outcome ที่ตอบ research question โดยตรง
□ Results รายงาน outcome หลักก่อน ไม่ฝังอยู่ใน secondary analyses
□ Discussion paragraph แรก summarize คำตอบของ research question
□ Conclusions กลับมาที่ research question และตอบว่า "พบอะไร?"
□ ถ้า research question ไม่ได้รับคำตอบ — ระบุเหตุผลและแสดงใน limitations

คำถามทดสอบ: "อ่าน research question แล้วข้ามไปอ่าน Conclusions — ตอบกันได้หรือไม่?"
```

ดูเกณฑ์การประเมินเพิ่มเติมใน `references/deep-review-criteria.md#C7`

---

#### C8 — Editor-Eye Test

```
ตรวจสอบในฐานะ Editor ที่อ่าน manuscript เป็นครั้งแรก:
□ Title — ชัดเจน, ดึงดูด, ระบุ study design และ population
□ Abstract — อ่านแล้วเข้าใจงานทั้งหมดได้ใน 30 วินาที
□ Scope fit — งานเหมาะกับ journal ที่ target (audience, scope, impact)
□ First paragraph ของ Introduction — น่าสนใจ, ระบุ importance ได้ทันที
□ ไม่มี formatting errors, ตัวย่อ undefined, หรือ incomplete sentences
□ Word count และ structure ตรง journal guidelines
□ Ethical statement, author contributions, conflicts of interest ครบ

คำถามทดสอบ: "ถ้าเป็น editor — จะ desk-reject หรือ send for review?"
เหตุผลที่ desk-reject: scope mismatch, poor writing, obvious methodological flaw,
duplicate submission signs, missing ethics statement
```

ดูเกณฑ์การประเมินเพิ่มเติมใน `references/deep-review-criteria.md#C8`

---

#### C9 — Reviewer Anticipation

```
ระบุ objections ที่ reviewers น่าจะมีและตรวจสอบว่า manuscript address ไว้แล้วหรือไม่:
□ "Why this design?" — Methods justify design choice หรือไม่?
□ "Why this sample size?" — Sample size calculation อธิบายไว้หรือไม่?
□ "What about confounders?" — Potential confounders ถูก address หรือ acknowledge
□ "Is the outcome clinically meaningful?" — Clinical significance อธิบายไว้หรือไม่?
□ "Are results generalizable?" — External validity discussedหรือไม่?
□ "Why no [standard analysis]?" — ถ้าขาด analysis ที่คาดหวัง — อธิบายเหตุผลไว้หรือไม่?

สร้าง: Anticipated Reviewer Objection List พร้อม status (Addressed / Partially / Missing)
```

ดูเกณฑ์การประเมินเพิ่มเติมใน `references/deep-review-criteria.md#C9`

---

#### C10 — Limitations Acknowledge แล้วหรือยัง

```
ตรวจสอบ:
□ Limitations section มีอยู่ใน Discussion (ไม่ใช่แค่ mention ผ่านๆ)
□ ระบุ limitations ที่เป็น "จริง" สำหรับ study นี้ (ไม่ใช่ generic boilerplate)
□ แต่ละ limitation อธิบายว่า impact ต่อ findings อย่างไร
□ Limitations ไม่ถูก "dilute" ด้วยการพูดถึง strengths มากเกินไปก่อน
□ ไม่ขัดแย้งกับ conclusions — ถ้า limitation รุนแรง conclusions ต้องระมัดระวัง
□ Suggestions for future research เชื่อมกับ limitations อย่างสมเหตุสมผล

ตรวจพิเศษ: Missing data / attrition / selection bias / measurement bias
ระบุไว้หรือไม่?
```

ดูเกณฑ์การประเมินเพิ่มเติมใน `references/deep-review-criteria.md#C10`

---

#### C11 — Methodology Justify เพียงพอไหม

```
ตรวจสอบ:
□ ทุก methodological choice สำคัญมี rationale ("we used X because Y")
□ Statistical methods เหมาะสมกับ data distribution และ study design
□ Cutpoints, thresholds, และ definitions มีอ้างอิงรองรับหรือ pre-specified
□ Sensitivity analyses ทำเมื่อ methodological assumptions อาจส่งผลต่อ results
□ Multiple testing / multiplicity address ไว้หรือไม่ (ถ้ามี secondary outcomes หลายตัว)
□ Protocol/SAP registration (ถ้ามี) — ผลตรงกับที่ pre-specified หรือไม่?

คำถามทดสอบ: "reviewer สามารถ replicate methods ทุกขั้นตอนจาก Methods section หรือไม่?"
```

ดูเกณฑ์การประเมินเพิ่มเติมใน `references/deep-review-criteria.md#C11`

---

#### C12 — Generalizability Claim สมเหตุสมผลกับ Sample/Context หรือไม่

```
ตรวจสอบ:
□ Conclusions ไม่ claim generalizability เกินกว่า study population
□ ถ้า single-center — acknowledge ใน limitations ว่า practice variation อาจต่างกัน
□ ถ้า population เฉพาะ (เช่น elderly, Asian, urban) — ระบุว่า results อาจไม่ apply กับ population อื่น
□ Sample context (ประเทศ, ช่วงเวลา, healthcare setting) ระบุไว้ใน Methods
□ External validity discussion เหมาะสม — ไม่ overstate ไม่ understate

เกณฑ์: claim ต้องสอดคล้องกับ internal validity ของ study
(observational → "may inform" / pilot → "hypothesis-generating" / large RCT → stronger claim)
```

ดูเกณฑ์การประเมินเพิ่มเติมใน `references/deep-review-criteria.md#C12`

---

### STEP 3 — Deep Review Report

แสดงผลลัพธ์ในรูปแบบมาตรฐาน:

```
╔══════════════════════════════════════════════════════════════╗
║          DEEP MANUSCRIPT QUALITY REVIEW REPORT               ║
╚══════════════════════════════════════════════════════════════╝

Manuscript: [ชื่อ / working title]
Reviewer Mode: Deep Quality Review (12 Criteria)
Date: [วันที่]

────────────────────────────────────────────────────────────────
SUMMARY SCORECARD
────────────────────────────────────────────────────────────────
C1  Research Gap Clarity          🟢/🟡/🔴  [one-line verdict]
C2  Novelty & Contribution        🟢/🟡/🔴  [one-line verdict]
C3  Design ↔ Question Alignment   🟢/🟡/🔴  [one-line verdict]
C4  Argument Flow                 🟢/🟡/🔴  [one-line verdict]
C5  No Overclaiming               🟢/🟡/🔴  [one-line verdict]
C6  All Claims Referenced         🟢/🟡/🔴  [one-line verdict]
C7  Structure ↔ RQ Alignment      🟢/🟡/🔴  [one-line verdict]
C8  Editor-Eye Test               🟢/🟡/🔴  [one-line verdict]
C9  Reviewer Anticipation         🟢/🟡/🔴  [one-line verdict]
C10 Limitations Acknowledged      🟢/🟡/🔴  [one-line verdict]
C11 Methodology Justified         🟢/🟡/🔴  [one-line verdict]
C12 Generalizability Appropriate  🟢/🟡/🔴  [one-line verdict]

Overall: X/12 PASS | Y REVISE | Z MAJOR
────────────────────────────────────────────────────────────────
PRIORITY ACTION LIST
────────────────────────────────────────────────────────────────
🔴 MAJOR (แก้ก่อน submit):
1. [C#] [ปัญหา] → [คำแนะนำเฉพาะเจาะจง พร้อมตัวอย่างการแก้ไข]

🟡 REVISE (ควรแก้เพื่อเพิ่มโอกาสตีพิมพ์):
1. [C#] [ปัญหา] → [คำแนะนำ]

🟢 STRENGTHS (จุดแข็งที่ควรรักษาไว้):
1. [C#] [สิ่งที่ทำได้ดี]
────────────────────────────────────────────────────────────────
ANTICIPATED REVIEWER OBJECTIONS (C9)
────────────────────────────────────────────────────────────────
[Objection 1] → Status: Addressed / Partially / ⚠️ Missing
[Objection 2] → Status: ...
────────────────────────────────────────────────────────────────
RECOMMENDATION
────────────────────────────────────────────────────────────────
□ Ready to submit as-is
□ Minor revisions needed before submission
□ Major revisions needed — revise and re-review
╚══════════════════════════════════════════════════════════════╝
```

---

### Auto-Trigger Mode 6 Suggestion

หลังจาก Mode 1 สร้าง draft เสร็จ หรือ Mode 4 ผ่าน QC แล้ว ให้แนะนำผู้ใช้:

```
✅ QC ผ่านแล้ว — แนะนำขั้นตอนถัดไป:

พิมพ์ "deep review" เพื่อให้ทำ Deep Manuscript Quality Review (12 criteria)
ก่อน submit จะช่วยระบุปัญหา conceptual ที่ reviewers มักตี paper กลับ
```

---

## Reporting Guidelines Checklist

### เรียก manuscript-master-workflow หลังสร้าง draft

หลังสร้าง draft manuscript เสร็จ แนะนำผู้ใช้ทุกครั้ง:

```
Draft เสร็จแล้วครับ ขั้นตอนถัดไปที่แนะนำ:

1. **Peer Review** (manuscript-master-workflow Mode B):
   ประเมิน 7 หมวด: Scope, Literature, Methods, Results, Discussion, Writing, Ethics

2. **Language Refinement** (manuscript-master-workflow Mode C):
   ปรับ academic English ให้เป็นธรรมชาติ

3. **Journal Strategy** (manuscript-master-workflow Mode D):
   เลือกวารสาร Plan A/B/C ที่เหมาะสม

พิมพ์ "evaluate manuscript", "improve language" หรือ "เลือก journal" เพื่อดำเนินการต่อ
```

---

## หลักการสำคัญ

- **อย่า** สร้างข้อมูล ผลลัพธ์ หรือ references ที่ไม่มีในข้อมูลจริง
- **ต้อง** ระบุเมื่อข้อมูลไม่เพียงพอแทนที่จะเดา
- **ต้อง** ปฏิบัติตาม reporting guideline (CONSORT/STROBE/STARD/TRIPOD)
- **ต้อง** แยก statistical significance ออกจาก clinical significance
- รักษา transparency ในการรายงาน limitations

## Reference Files
| File | เนื้อหา |
|---|---|
| `references/reporting-guidelines.md` | CONSORT/STROBE/STARD/TRIPOD checklist |
| `references/imrad-structure.md` | IMRAD templates และ section-by-section guide |
| `references/consistency-qc-checklist.md` | Checklist ตรวจสอบ manuscript ↔ tables/figures และ figure label QC |
| `references/stata-figure-style.md` | STATA-style Python theme, color palette, และ templates ครบทุก figure type |
| `references/citation-search-strategy.md` | กลยุทธ์ค้นหาอ้างอิง Q1/Q2 ผ่าน PubMed, Consensus, Scholar Gateway, Web of Science |
| `references/deep-review-criteria.md` | รายละเอียดเชิงลึกของ 12 criteria สำหรับ Deep Manuscript Quality Review (Mode 6) |
