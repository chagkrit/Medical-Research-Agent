# Consistency & Figure QC Checklist
# คู่มือตรวจสอบความสอดคล้อง Manuscript ↔ Tables/Figures และ Figure Label Quality

---

## Section 1: Manuscript ↔ Tables/Figures Consistency

### 1.1 Text-to-Table/Figure Citation Check

ตรวจสอบว่า **ทุก table/figure ที่อ้างถึงใน text มีอยู่จริง** และ **ทุก table/figure ที่มีอยู่ถูกอ้างถึงใน text**

```
□ Table 1 — อ้างถึงใน text ✓ / ✗ | มีอยู่จริง ✓ / ✗
□ Table 2 — อ้างถึงใน text ✓ / ✗ | มีอยู่จริง ✓ / ✗
□ Figure 1 — อ้างถึงใน text ✓ / ✗ | มีอยู่จริง ✓ / ✗
□ Figure 2 — อ้างถึงใน text ✓ / ✗ | มีอยู่จริง ✓ / ✗
(เพิ่มตามจำนวนจริง)
```

### 1.2 Numerical Consistency Check

ตรวจสอบตัวเลขสำคัญทุกตัวที่รายงานใน Results section:

| ค่าที่ตรวจสอบ | ค่าใน Text | ค่าใน Table/Figure | ตรงกัน? |
|---|---|---|---|
| N total | | | ✓/✗ |
| Primary outcome % หรือ mean | | | ✓/✗ |
| p-value (primary) | | | ✓/✗ |
| OR/HR/RR + 95% CI | | | ✓/✗ |
| AUC (ถ้ามี) | | | ✓/✗ |
| Median OS/PFS (ถ้ามี) | | | ✓/✗ |

**กฎทอง:**
- ค่าใน Abstract ต้องตรงกับ Results section และ Tables/Figures **ทุกตัว**
- ทศนิยมต้องสอดคล้องกัน (e.g., ใช้ทศนิยม 1 ตำแหน่งทั้ง text และ table)
- หน่วยต้องระบุครบ (%, mmHg, mg/dL ฯลฯ)

### 1.3 Directionality Check

ตรวจสอบว่า "ทิศทาง" ของผลในข้อความสอดคล้องกับ table/figure:

```
□ ถ้า text บอกว่า "significant increase" → table/figure ต้องแสดงค่าที่สูงกว่าอย่างมีนัยสำคัญ
□ ถ้า text บอก "no significant difference" → p ≥ 0.05 ใน table
□ ถ้า text บอก "higher in group A" → group A มีค่าสูงกว่า group B จริงใน table
□ ผล subgroup ที่กล่าวถึงใน Discussion ตรงกับ Supplementary Table
```

### 1.4 Numbering Sequence Check

```
□ Tables ถูก numbered ตามลำดับที่ปรากฏครั้งแรกใน text (Table 1 → 2 → 3)
□ Figures ถูก numbered ตามลำดับที่ปรากฏครั้งแรกใน text (Figure 1 → 2 → 3)
□ Supplementary Tables/Figures แยก numbering (Supplementary Table 1, 2, ...)
□ ไม่มี "Table 4" ที่ปรากฏก่อน "Table 3" ใน text
```

### 1.5 Caption & Legend Consistency

```
□ Table title ใน manuscript ตรงกับที่ระบุใน actual table
□ Figure caption อธิบายครบ: abbreviations, n, statistical test ที่ใช้
□ ใน text กล่าวถึง figure/table ด้วยชื่อที่ถูกต้อง (ไม่ใช้ "the figure above")
```

---

## Section 2: Figure Label Quality Checklist

### 2.1 Universal Label Rules (ทุก Figure ต้องผ่าน)

```
□ X-axis title: มีและอ่านได้ชัดเจน (ไม่ถูก clip หรือ truncate)
□ Y-axis title: มีและอ่านได้ชัดเจน (ไม่ถูก clip หรือ truncate)
□ Axis tick labels: ไม่ทับกัน, ไม่ทับกับ axis title
□ Legend: มี (ถ้าจำเป็น), ไม่ทับซ้อนกับ data
□ Figure title: ไม่ทับกับ plot area
□ plt.tight_layout() หรือ fig.tight_layout() ถูกเรียกใช้
□ DPI ≥ 300 สำหรับ publication (plt.savefig(..., dpi=300))
□ Font size อ่านได้ (≥ 8pt สำหรับ publication, ≥ 10pt แนะนำ)
```

### 2.2 Specific Figure Type Checks

#### Kaplan-Meier Curve
```
□ Time axis (x): หน่วยระบุชัด (months/years)
□ Survival probability axis (y): ช่วง 0–1.0 หรือ 0–100%
□ At-risk table (ถ้ามี): ไม่ทับกับ curve
□ Censoring marks: เล็กพอที่ไม่บดบัง curve
□ Group labels ใน legend: ชัดเจน, ไม่ทับกัน
□ p-value / log-rank: ระบุตำแหน่งที่ไม่ทับ curve (upper right หรือ inset box)
□ Median survival time annotations: ไม่ทับกับ curve อื่น
```

#### ROC Curve
```
□ แกน x: "1 - Specificity" หรือ "False Positive Rate"
□ แกน y: "Sensitivity" หรือ "True Positive Rate"
□ Diagonal reference line: มี (dashed)
□ AUC annotation: อยู่ใน plot area, ไม่ทับ curve
□ Multiple curves (ถ้ามี): legend ชัดเจน, ไม่ทับกัน, สีแตกต่างกันพอ
□ Optimal cutpoint marker (ถ้ามี): ไม่ทับกับ curve label
```

#### Forest Plot
```
□ Study labels (y-axis): อ่านได้ทั้งหมด, ไม่ truncate
□ Effect size column: align ถูกต้อง
□ CI bars: ไม่ทับกัน (ถ้า studies มาก ปรับ figsize)
□ Diamond (overall effect): ไม่ทับกับ CI bar สุดท้าย
□ Vertical reference line (null = 1.0 หรือ 0): ชัดเจน, labeled
□ Column headers: ไม่ทับกับ data
□ Subgroup labels: indented และ bold ชัดเจน
```

#### Bar/Column Chart
```
□ Bar labels (ถ้ามี): อยู่เหนือ bar, ไม่ถูก clip โดย y-axis limit
□ Error bars: visible, cap size เหมาะสม
□ Statistical brackets (p-value): ไม่ทับกัน, มีระยะห่างจาก bar
□ X-tick labels: หมุนถ้ายาว (rotation=45° พร้อม ha='right')
□ Group labels (grouped bar): ชัดเจนใต้แต่ละ group
□ Y-axis limit: สูงพอสำหรับ error bars + annotations
```

#### Scatter Plot
```
□ Data point labels (ถ้ามี): ใช้ adjustText เพื่อป้องกัน overlap
□ Trend line label: ไม่ทับ data points
□ Correlation annotation (r, p): อยู่ใน corner ที่โล่ง
□ Outlier labels: ชัดเจน, มีเส้นชี้ถ้าจำเป็น
```

### 2.3 Color & Accessibility Check
```
□ สีแตกต่างกันพอสำหรับ colorblind (ใช้ palette: 'colorblind' หรือ 'tab10')
□ ไม่ใช้ red/green เป็นคู่เดียว (colorblind-unfriendly)
□ Grayscale printable: ถ้าพิมพ์ขาวดำ ยังอ่านได้ (ใช้ pattern/linestyle เสริม)
□ Background สีขาวหรือ off-white (ไม่ใช้ dark background สำหรับ journal)
```

---

## Section 3: Common Errors & Quick Fixes

| ปัญหา | สาเหตุ | วิธีแก้ไข |
|---|---|---|
| X-axis tick labels ทับกัน | Labels ยาว หรือ ticks หนาแน่น | `plt.xticks(rotation=45, ha='right')` + `plt.tight_layout()` |
| Legend ทับ data | Legend size ใหญ่ | `ax.legend(bbox_to_anchor=(1.05, 1), loc='upper left')` |
| Y-axis label ถูก clip | Figure margin ซ้ายแคบ | `plt.subplots_adjust(left=0.15)` |
| Annotation ทับกัน | Multiple labels หนาแน่น | ใช้ `adjustText` library |
| p-value bracket ทับ bar | Y-limit ต่ำเกินไป | `ax.set_ylim(0, max_val * 1.3)` |
| Title ทับ plot | ไม่มี padding | `plt.title(..., pad=15)` |
| Text ถูก clip ขอบ figure | tight_layout ไม่ถูกเรียก | `plt.tight_layout(pad=1.5)` |
| At-risk table ทับ KM curve | Subplot spacing | `plt.subplots_adjust(bottom=0.25)` |

---

## Section 4: Final Publication-Ready Checklist

ก่อน submit manuscript ตรวจสอบ:

```
□ ทุก Figure ส่งออกเป็น TIFF หรือ EPS ที่ DPI ≥ 300
□ Figure dimensions ตาม journal guidelines (single/double column)
□ Font ใน figure เป็น Arial, Helvetica, หรือ Times New Roman
□ ไม่มี font ที่ embed เป็น bitmap
□ Color mode: RGB (ไม่ใช่ CMYK ถ้า journal ไม่ระบุ)
□ Figure numbering ตรงกับ manuscript text ทุกตัว
□ Supplementary figures แยกไฟล์ตาม journal requirements
□ Figure captions อยู่ใน manuscript text (ไม่ใช่ embed ใน image file)
```
