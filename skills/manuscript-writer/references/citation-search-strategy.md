# Citation Search Strategy for Introduction & Discussion
# กลยุทธ์การค้นหาเอกสารอ้างอิง Q1/Q2 สำหรับ Introduction และ Discussion

---

## ภาพรวมลำดับความสำคัญของแหล่งอ้างอิง

```
ลำดับการค้นหา (Priority Order):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1️⃣  PubMed           → biomedical literature ครบครัน, indexed, peer-reviewed
2️⃣  Consensus        → AI-curated, เน้นหา consensus จากหลายบทความ
3️⃣  Scholar Gateway  → ครอบคลุม Google Scholar + Semantic Scholar
4️⃣  Web of Science   → Journal Quartile ranking, impact factor (manual)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

คุณภาพ Journal (เรียงจากสูงไปต่ำ):
Q1 (ลำดับแรก) → Q2 (ลำดับสอง) → Q3/Q4 (ใช้เฉพาะเมื่อจำเป็น)
```

---

## 1. PubMed Search Strategy (แหล่งหลัก — ใช้ก่อนเสมอ)

### MCP Tool: `search_articles` (PubMed MCP — connected)

```
ค้นหาด้วย keyword หลักของงานวิจัย เช่น:
- "[condition] AND [intervention] AND [outcome]"
- "[disease] AND [biomarker] AND [prognosis]"
- "[surgery type] AND [complication] AND [survival]"

Document types ที่ต้องการ (เรียงตามน้ำหนัก evidence):
1. Meta-Analysis / Systematic Review  ← ใช้ก่อน
2. Randomized Controlled Trial (RCT)
3. Cohort Study / Prospective Study
4. Case-Control Study
5. Cross-sectional Study

Date range: ย้อนหลัง 5-10 ปี (ยกเว้น landmark papers)
Language: English
```

### PubMed MeSH Terms ตาม Section

```
สำหรับ Introduction — Paragraph 1 (Background/Epidemiology):
- "[disease][MeSH] AND incidence[MeSH] AND prevalence"
- "[disease][MeSH] AND mortality AND morbidity"
- "[condition] AND burden AND global"

สำหรับ Introduction — Paragraph 2 (Gap/Limitations):
- "[topic] AND limitations OR knowledge gap"
- "[current treatment] AND outcomes AND failure"
- "[method/approach] AND compared OR versus"

สำหรับ Introduction — Paragraph 3 (Rationale):
- "[hypothesis area] AND evidence AND rationale"

สำหรับ Discussion — Paragraph 2 (Context/Comparison):
- "[similar study design] AND [same population]"
- "[same intervention] AND [same outcome]"
- "[conflicting evidence] AND [topic]"

สำหรับ Discussion — Paragraph 3 (Mechanism):
- "[biological pathway] AND [disease mechanism]"
- "[preclinical evidence] AND [mechanism]"
```

### เกณฑ์คัดเลือกจาก PubMed

```
✅ ควรเลือก:
□ Journal อยู่ใน Q1 หรือ Q2 (ตรวจสอบที่ scimagojr.com)
□ Study design เหมาะสม ตาม evidence hierarchy ข้างต้น
□ Population และ setting ใกล้เคียงกับงานของเรา
□ ตีพิมพ์ใน 5 ปีล่าสุด (ยกเว้น landmark/classic papers)
□ Citation count สูงสำหรับอายุบทความ

❌ หลีกเลี่ยง:
□ Journal ที่ไม่อยู่ใน Q1/Q2 (ยกเว้นจำเป็นจริงๆ)
□ Case report / Case series สำหรับ epidemiology claims
□ Conference abstract ที่ยังไม่ได้ตีพิมพ์
□ Predatory journals (ตรวจสอบ Beall's List)
```

---

## 2. Consensus Search (แหล่งที่สอง)

### MCP Tool: `search` (Consensus MCP — connected)

```
Consensus เหมาะสำหรับ:
- ตรวจสอบ "consensus" จากงานวิจัยหลายชิ้น
- หา systematic evidence สำหรับ claim ใน Introduction/Discussion
- ตรวจสอบว่า evidence "agree" หรือ "disagree" กัน

ตัวอย่างคำค้น:
- "What is the effect of [intervention] on [outcome] in [population]?"
- "Does [biomarker] predict [outcome] in [disease]?"
- "Is [treatment] effective for [condition]?"

การอ่านผล:
- Consensus Meter สูง → evidence เห็นพ้องต้องกัน → อ้างได้มั่นใจ
- Consensus Meter ต่ำ → evidence ขัดแย้ง → ต้องอ้างทั้ง 2 ด้านใน Discussion
```

---

## 3. Scholar Gateway — Google Scholar / Semantic Scholar (แหล่งที่สาม)

### MCP Tool: `Semantic Search` (Scholar Gateway MCP — connected)

```
Scholar Gateway เหมาะสำหรับ:
- ค้นหา semantic (ความหมาย) ไม่ใช่แค่ keyword matching
- หา highly-cited papers ที่อาจไม่อยู่ใน PubMed
- ค้นหา landmark/classic papers ที่ต้องอ้าง

ตัวอย่างการใช้:
- ค้นด้วยประโยค: "prognostic factors for recurrence in breast cancer surgery"
- ค้นด้วย concept: "machine learning predict surgical outcomes"
- ค้น landmark paper: "original description of [technique/concept]"

เกณฑ์คัดเลือก:
✅ Citation count สูง (> 50 citations สำหรับงานล่าสุด, > 200 สำหรับงานเก่ากว่า 5 ปี)
✅ Journal อยู่ใน Q1/Q2
✅ ใช้ cross-validate กับผลจาก PubMed
```

---

## 4. Web of Science (แหล่งที่สี่ — ตรวจสอบ Quartile)

```
Web of Science ไม่มี MCP connector โดยตรง
→ แนะนำให้ผู้ใช้ตรวจสอบด้วยตนเอง

ทางเลือกฟรี — Scimago Journal Rank (SJR):
URL: https://www.scimagojr.com
→ ค้นชื่อวารสาร → ดู Best Quartile ในสาขา
→ Q1 = top 25%, Q2 = ลำดับ 25-50%

วิธีค้นใน Web of Science (ถ้ามี access):
1. Topic Search ด้วย keyword
2. Filter: Document type = Article / Review
3. Sort by: Times Cited (descending)
4. ดู Journal Citation Reports (JCR) quartile
```

---

## 5. เกณฑ์ประเมิน Q1/Q2 Journal

### Journal Priority Tiers

```
TIER 1 — Q1 Journals (ใช้ก่อนเสมอ):
General: NEJM, Lancet, JAMA, BMJ, Nature Medicine, PLOS Medicine
Surgery: Annals of Surgery, JAMA Surgery, BJS, Ann Surg Oncol
Oncology: J Clin Oncol, Cancer, Lancet Oncology, Cancer Medicine
Breast: Breast Cancer Research, Breast Cancer Res Treat
Critical Care: Crit Care Med, Intensive Care Med
Radiology: Radiology, Eur Radiol, AJR

TIER 2 — Q2 Journals (ใช้เมื่อ Q1 ไม่ครอบคลุม):
วารสาร IF 2.0-5.0 ในสาขา
Regional journals ที่มีคุณภาพสูง
ใช้เมื่อ Q1 ไม่มี evidence สำหรับประเด็นนั้น

TIER 3 — Q3/Q4 (ใช้เมื่อจำเป็นจริงๆ เท่านั้น):
ใช้เฉพาะ landmark paper หรือไม่มีทางเลือกอื่น
แจ้งผู้ใช้ทุกครั้งเมื่อต้องใช้ Q3/Q4
```

---

## 6. จำนวน References ที่เหมาะสม

```
Introduction (3 paragraphs):
- Paragraph 1 (Background):  3-5 refs → Q1 เป็นหลัก
- Paragraph 2 (Gap):         3-5 refs → Q1/Q2
- Paragraph 3 (Rationale):   1-2 refs → Q1

Discussion (5 paragraphs):
- Paragraph 1 (Summary):     0-1 ref
- Paragraph 2 (Context):     4-6 refs → Q1/Q2 direct comparison
- Paragraph 3 (Mechanism):   2-4 refs → Q1/Q2
- Paragraph 4 (Limitations): 1-2 refs
- Paragraph 5 (Conclusions): 0-2 refs

รวม Introduction + Discussion: ~20-30 references (Q1/Q2 ≥ 80%)
รวมทั้ง manuscript: ~30-50 references
```

---

## 7. Citation Format — Vancouver Standard

```
รูปแบบมาตรฐาน (วารสารทางการแพทย์):
1. Smith AB, Jones CD, Lee EF, et al. Title of article. J Med Abbrev. 2023;45(3):123-30.
   doi:10.xxxx/xxxxx [PMID: xxxxxxxx]

กฎสำคัญ:
- ≤ 6 authors: ใส่ทุกคน
- > 6 authors: ใส่ 6 คน + "et al."
- ใช้ PubMed MEDLINE abbreviations สำหรับชื่อวารสาร
- ระบุ DOI ทุกครั้งที่มี
- ตรวจสอบ accuracy ของ author names, year, volume, pages
```

---

## 8. Workflow สรุป — ขั้นตอนค้นหาอ้างอิง

```
STEP 1: ระบุ claim ที่ต้องอ้างอิง
  → "ต้องการอ้างว่า [X] ใน [population] มี [outcome] = [Y]"

STEP 2: ค้น PubMed ก่อน (MCP: search_articles)
  → keywords: "[topic] AND [outcome] AND [population]"
  → filter: Meta-Analysis/Systematic Review/RCT ก่อน

STEP 3: หากยังไม่พอ → Consensus (MCP: search)
  → ถาม: "Does [X] affect [Y] in [Z]?"
  → ดู Consensus Meter + key papers

STEP 4: ถ้าต้องการ high-citation landmark papers → Scholar Gateway (MCP: Semantic Search)
  → ค้นด้วย phrase/concept

STEP 5: ตรวจสอบ Journal Quartile
  → เปิด https://www.scimagojr.com → ค้นชื่อวารสาร → ดู Q
  → เลือก Q1 ก่อน ถ้าไม่พอจึงใช้ Q2

STEP 6: จัดรูปแบบ references ใน Vancouver format
  → ตรวจสอบ accuracy ของข้อมูลทุกตัวเลข/ผล
  → แจ้งผู้ใช้ถ้าต้องใช้ Q3/Q4
```
