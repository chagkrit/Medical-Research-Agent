# Group Balancing Methods — Medical Research Reference
# วิธีปรับสมดุลกลุ่มสำหรับงานวิจัยเชิงสังเกต

> **เมื่อไหร่ต้องใช้**: เมื่อเปรียบเทียบสองกลุ่มใน observational study แล้วพบว่า baseline characteristics แตกต่างกัน (SMD > 0.1 หรือ p < 0.05 ใน Table 1)

---

## 0. การเลือกวิธีที่เหมาะสม

| วิธี | เหมาะกับ | ข้อดี | ข้อเสีย |
|---|---|---|---|
| **Propensity Score Matching (PSM)** | n ปานกลาง (200–2000), confounders ≤ 10 | ตีความง่าย, มาตรฐานในงานวิจัย | ทิ้ง unmatched → ลด sample size |
| **IPTW** (Inverse Probability of Treatment Weighting) | n ใหญ่, ต้องการ preserve ทุก observation | ไม่ทิ้งข้อมูล, เหมาะ survival analysis | อาจมี extreme weights → ต้อง trim |
| **Entropy Balancing** | confounders > 10, ต้องการ balance แม่นยำ | balance ดีที่สุด, ไม่ต้อง trim weights | ต้องการ package เพิ่มเติม |
| **CEM** (Coarsened Exact Matching) | ข้อมูลเป็น categorical หลัก | exact match, ชัดเจน | ข้อมูลสูญหายมากถ้า coarsening แรง |
| **ANCOVA** | RCT ที่มี baseline imbalance เล็กน้อย | ง่าย, ไม่ต้อง match | ไม่เหมาะ observational study ที่ confounding รุนแรง |

---

## 1. Propensity Score Matching (PSM) — แนะนำอันดับ 1

### 1.1 Estimate Propensity Score

```python
import pandas as pd
import numpy as np
from sklearn.linear_model import LogisticRegression
from sklearn.preprocessing import StandardScaler

# confounders = รายชื่อตัวแปรที่ต้องการ balance
# treatment_col = ชื่อ column ที่แบ่งกลุ่ม (1=treatment, 0=control)

confounders = ['age', 'sex', 'bmi', 'diabetes', 'hypertension', 'smoking']
treatment_col = 'treatment'

# Prepare data
X = df[confounders].copy()
y = df[treatment_col]

# Handle missing values ก่อน
X = X.fillna(X.median())

# Scale continuous variables
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# Logistic regression → propensity score
ps_model = LogisticRegression(
    max_iter=1000,
    C=1.0,           # regularization
    random_state=42
)
ps_model.fit(X_scaled, y)
df['ps'] = ps_model.predict_proba(X_scaled)[:, 1]

# ตรวจสอบ common support
print(f"Treatment group PS: {df[df[treatment_col]==1]['ps'].describe()}")
print(f"Control group PS:   {df[df[treatment_col]==0]['ps'].describe()}")

# Overlap plot (ต้องมี overlap ที่ดี)
import matplotlib.pyplot as plt
fig, ax = plt.subplots(figsize=(8, 4))
ax.hist(df[df[treatment_col]==1]['ps'], bins=30, alpha=0.6, label='Treatment', color='#2171b5')
ax.hist(df[df[treatment_col]==0]['ps'], bins=30, alpha=0.6, label='Control', color='#cb181d')
ax.set_xlabel('Propensity Score')
ax.set_ylabel('Count')
ax.set_title('Propensity Score Distribution (Common Support Check)')
ax.legend()
plt.tight_layout()
plt.savefig('figure_ps_overlap.tiff', dpi=300, bbox_inches='tight')
plt.show()
```

### 1.2 Nearest Neighbor Matching (1:1 without replacement)

```python
from scipy.spatial.distance import cdist

def psm_nearest_neighbor(df, treatment_col, ps_col, caliper=0.2):
    """
    1:1 Nearest Neighbor Matching with caliper
    caliper = 0.2 * SD ของ propensity score (Austin 2011)
    """
    treated = df[df[treatment_col] == 1].copy()
    control = df[df[treatment_col] == 0].copy()

    # Caliper = 0.2 * pooled SD ของ logit(PS)
    df['logit_ps'] = np.log(df[ps_col] / (1 - df[ps_col]))
    caliper_value = caliper * df['logit_ps'].std()
    print(f"Caliper (logit PS): {caliper_value:.4f}")

    matched_control_idx = []
    used_controls = set()

    treated_logit = np.log(treated[ps_col] / (1 - treated[ps_col])).values
    control_logit = np.log(control[ps_col] / (1 - control[ps_col])).values

    for i, t_ps in enumerate(treated_logit):
        # คำนวณ distance กับทุก control
        distances = np.abs(control_logit - t_ps)

        # เรียงลำดับ distance
        sorted_idx = np.argsort(distances)

        matched = False
        for idx in sorted_idx:
            if idx not in used_controls and distances[idx] <= caliper_value:
                matched_control_idx.append(control.index[idx])
                used_controls.add(idx)
                matched = True
                break

        if not matched:
            matched_control_idx.append(None)

    # สร้าง matched dataset
    treated['matched_control'] = matched_control_idx
    treated_matched = treated[treated['matched_control'].notna()]
    control_matched = df.loc[treated_matched['matched_control'].values]

    df_matched = pd.concat([treated_matched, control_matched])

    print(f"\nMatching Results:")
    print(f"  Treatment N: {len(treated)} → {len(treated_matched)} matched")
    print(f"  Control N:   {len(control)} → {len(control_matched)} matched")
    print(f"  Match rate:  {len(treated_matched)/len(treated)*100:.1f}%")

    return df_matched

# Run matching
df_matched = psm_nearest_neighbor(df, treatment_col='treatment', ps_col='ps', caliper=0.2)
```

### 1.3 ตรวจสอบ Balance หลัง Matching

```python
def check_balance(df_before, df_after, treatment_col, confounders):
    """
    เปรียบเทียบ SMD ก่อนและหลัง matching
    """
    from medical_stats import compute_smd  # ฟังก์ชันจาก SKILL.md

    results = []

    for var in confounders:
        # ก่อน matching
        g1_before = df_before[df_before[treatment_col]==1][var].dropna()
        g0_before = df_before[df_before[treatment_col]==0][var].dropna()

        # หลัง matching
        g1_after = df_after[df_after[treatment_col]==1][var].dropna()
        g0_after = df_after[df_after[treatment_col]==0][var].dropna()

        # ตรวจว่า continuous หรือ binary
        is_cont = df_before[var].nunique() > 5

        smd_before = compute_smd(g1_before, g0_before, is_continuous=is_cont)
        smd_after  = compute_smd(g1_after,  g0_after,  is_continuous=is_cont)

        results.append({
            'Variable': var,
            'SMD Before': round(smd_before, 3),
            'SMD After':  round(smd_after, 3),
            'Improved': '✅' if smd_after < smd_before else '❌',
            'Balanced': '✅' if smd_after < 0.1 else ('⚠️' if smd_after < 0.2 else '❌')
        })

    balance_df = pd.DataFrame(results)
    print(balance_df.to_string(index=False))
    print(f"\nMax SMD after: {balance_df['SMD After'].max():.3f} "
          f"(target < 0.1)")

    return balance_df
```

---

## 2. Love Plot — แสดง Balance ก่อนและหลัง (STATA style)

```python
def plot_love_stata(balance_df, save_path='figure_love_plot.tiff'):
    """
    Love Plot: แสดง SMD ก่อนและหลัง balancing
    เส้นประแดง = SMD 0.1 threshold
    """
    import matplotlib.pyplot as plt
    import matplotlib.patches as mpatches

    fig, ax = plt.subplots(figsize=(8, max(5, len(balance_df) * 0.45)))

    y_pos = np.arange(len(balance_df))
    vars_labels = balance_df['Variable'].tolist()

    # Plot SMD before (วงกลมเปิด)
    ax.scatter(balance_df['SMD Before'], y_pos,
               color='#cb181d', marker='o', s=80, zorder=3,
               facecolors='none', linewidths=2, label='Before Balancing')

    # Plot SMD after (วงกลมทึบ)
    ax.scatter(balance_df['SMD After'], y_pos,
               color='#2171b5', marker='o', s=80, zorder=3,
               label='After Balancing')

    # เส้นเชื่อม before-after
    for i in range(len(balance_df)):
        ax.plot([balance_df['SMD Before'].iloc[i], balance_df['SMD After'].iloc[i]],
                [y_pos[i], y_pos[i]], color='gray', alpha=0.5, linewidth=1)

    # Threshold lines
    ax.axvline(x=0.1, color='black', linestyle='--', linewidth=1.2,
               label='SMD = 0.1 threshold', zorder=2)
    ax.axvline(x=0.0, color='black', linestyle='-', linewidth=0.8, zorder=2)

    # Formatting (STATA style)
    ax.set_yticks(y_pos)
    ax.set_yticklabels(vars_labels, fontsize=10)
    ax.set_xlabel('Standardized Mean Difference (SMD)', fontsize=11)
    ax.set_title('Love Plot: Covariate Balance Before and After Balancing',
                 fontsize=12, fontweight='bold', pad=12)
    ax.legend(loc='lower right', fontsize=9, framealpha=0.9)
    ax.grid(axis='x', alpha=0.3)
    ax.spines['top'].set_visible(False)
    ax.spines['right'].set_visible(False)

    # Background shade สำหรับ balanced zone
    ax.axvspan(0, 0.1, alpha=0.06, color='green')

    plt.tight_layout()
    plt.savefig(save_path, dpi=300, bbox_inches='tight')
    plt.show()
    print(f"Love Plot saved: {save_path}")

# ใช้งาน:
balance_df = check_balance(df, df_matched, 'treatment', confounders)
plot_love_stata(balance_df, save_path='figure_love_plot.tiff')
```

---

## 3. IPTW — Inverse Probability of Treatment Weighting

> **เหมาะกับ**: n ใหญ่, ต้องการ preserve ทุก observation, survival analysis

```python
# ใช้ propensity score ที่ estimate ไว้แล้ว (Section 1.1)

# คำนวณ weights
df['iptw'] = np.where(
    df['treatment'] == 1,
    1 / df['ps'],           # treated: 1/PS
    1 / (1 - df['ps'])      # control: 1/(1-PS)
)

# Stabilized weights (แนะนำ — ลด extreme weights)
p_treat = df['treatment'].mean()
df['iptw_stabilized'] = np.where(
    df['treatment'] == 1,
    p_treat / df['ps'],
    (1 - p_treat) / (1 - df['ps'])
)

# ตรวจ extreme weights → ต้อง trim ถ้า max > 10
print(f"Weight summary:")
print(df['iptw_stabilized'].describe())
print(f"Extreme weights (>10): {(df['iptw_stabilized'] > 10).sum()}")

# Trim weights ที่ percentile 1 และ 99
w_p01 = df['iptw_stabilized'].quantile(0.01)
w_p99 = df['iptw_stabilized'].quantile(0.99)
df['iptw_trimmed'] = df['iptw_stabilized'].clip(w_p01, w_p99)

# ─── Weighted Analysis ────────────────────────────────────────────────────
import statsmodels.api as sm

# Weighted logistic regression
model_iptw = sm.WLS(
    df['outcome'],
    sm.add_constant(df[['treatment'] + confounders]),
    weights=df['iptw_trimmed']
).fit()

# Weighted survival analysis
from lifelines import CoxPHFitter

cph_iptw = CoxPHFitter()
cph_iptw.fit(
    df[['time', 'event', 'treatment'] + confounders],
    duration_col='time',
    event_col='event',
    weights_col='iptw_trimmed',    # ← ใส่ weights ที่นี่
    robust=True                     # robust SE สำหรับ weighted analysis
)
cph_iptw.print_summary()
```

---

## 4. Entropy Balancing

> **เหมาะกับ**: confounders เยอะ, ต้องการ balance แม่นยำที่สุด (Hainmueller 2012)

```python
# pip install ebal --break-system-packages
# หรือใช้ R-style via rpy2

# Python implementation (manual entropy balancing)
from scipy.optimize import minimize

def entropy_balancing(X_treat, X_control, moments=1):
    """
    คำนวณ entropy balancing weights สำหรับ control group
    เพื่อให้ weighted means = means ของ treatment group

    moments=1: match means only
    moments=2: match means and variances
    """
    n_control = len(X_control)
    target_means = X_treat.mean(axis=0)

    def objective(log_weights):
        weights = np.exp(log_weights)
        weights = weights / weights.sum()
        return np.sum(weights * log_weights)  # entropy

    def constraint_means(log_weights):
        weights = np.exp(log_weights)
        weights = weights / weights.sum()
        return (X_control * weights[:, np.newaxis]).sum(axis=0) - target_means

    # Initial weights (uniform)
    log_w0 = np.zeros(n_control)

    constraints = [{'type': 'eq', 'fun': constraint_means}]
    if moments == 2:
        target_vars = X_treat.var(axis=0)
        def constraint_vars(log_weights):
            weights = np.exp(log_weights)
            weights = weights / weights.sum()
            weighted_means = (X_control * weights[:, np.newaxis]).sum(axis=0)
            weighted_vars = (weights[:, np.newaxis] * (X_control - weighted_means)**2).sum(axis=0)
            return weighted_vars - target_vars
        constraints.append({'type': 'eq', 'fun': constraint_vars})

    result = minimize(
        objective, log_w0,
        method='SLSQP',
        constraints=constraints,
        options={'maxiter': 1000, 'ftol': 1e-10}
    )

    weights = np.exp(result.x)
    weights = weights / weights.sum() * n_control  # normalize
    return weights

# ใช้งาน
from sklearn.preprocessing import StandardScaler

X = df[confounders].fillna(df[confounders].median())
X_scaled = StandardScaler().fit_transform(X)

X_treat   = X_scaled[df['treatment'] == 1]
X_control = X_scaled[df['treatment'] == 0]

eb_weights = entropy_balancing(X_treat, X_control, moments=1)

# ใส่ weights กลับใน dataframe
df.loc[df['treatment'] == 0, 'eb_weight'] = eb_weights
df.loc[df['treatment'] == 1, 'eb_weight'] = 1.0  # treatment group weight = 1

print(f"Entropy Balancing weights: min={eb_weights.min():.3f}, "
      f"max={eb_weights.max():.3f}, mean={eb_weights.mean():.3f}")
```

---

## 5. Coarsened Exact Matching (CEM)

> **เหมาะกับ**: ต้องการ exact match ในตัวแปรสำคัญ, ข้อมูลเป็น categorical

```python
# pip install cem --break-system-packages
# หรือ manual CEM

def cem_matching(df, treatment_col, exact_vars, coarsen_vars=None, bins=5):
    """
    Coarsened Exact Matching
    exact_vars: ตัวแปรที่ต้องการ exact match (categorical)
    coarsen_vars: ตัวแปรที่ต้องการ coarsen ก่อน match (continuous)
    """
    df_cem = df.copy()

    # Coarsen continuous variables
    if coarsen_vars:
        for var in coarsen_vars:
            df_cem[f'{var}_bin'] = pd.cut(df_cem[var], bins=bins, labels=False)

    # สร้าง strata key
    match_vars = exact_vars + ([f'{v}_bin' for v in coarsen_vars] if coarsen_vars else [])
    df_cem['strata'] = df_cem[match_vars].astype(str).apply(
        lambda x: '_'.join(x), axis=1
    )

    # เก็บเฉพาะ strata ที่มีทั้ง treated และ control
    strata_treated = set(df_cem[df_cem[treatment_col]==1]['strata'])
    strata_control = set(df_cem[df_cem[treatment_col]==0]['strata'])
    common_strata  = strata_treated & strata_control

    df_matched_cem = df_cem[df_cem['strata'].isin(common_strata)]

    # CEM weights (proportional)
    strata_counts = df_matched_cem.groupby(['strata', treatment_col]).size().unstack()
    strata_counts.columns = ['n_control', 'n_treated']
    strata_counts['weight_control'] = strata_counts['n_treated'] / strata_counts['n_control']
    strata_counts['weight_treated'] = 1.0

    df_matched_cem = df_matched_cem.merge(
        strata_counts[['weight_control', 'weight_treated']],
        on='strata', how='left'
    )
    df_matched_cem['cem_weight'] = np.where(
        df_matched_cem[treatment_col] == 1,
        df_matched_cem['weight_treated'],
        df_matched_cem['weight_control']
    )

    print(f"CEM Results:")
    print(f"  Common strata: {len(common_strata)}")
    print(f"  Matched N:     {len(df_matched_cem)}")
    print(f"  Pruned N:      {len(df) - len(df_matched_cem)}")

    return df_matched_cem

# ใช้งาน
df_cem = cem_matching(
    df,
    treatment_col='treatment',
    exact_vars=['sex', 'diabetes'],         # ต้อง match ตรงๆ
    coarsen_vars=['age', 'bmi'],            # coarsen ก่อน
    bins=5
)
```

---

## 6. ANCOVA — สำหรับ RCT ที่มี Baseline Imbalance

> **ใช้เมื่อ**: RCT ที่มี baseline characteristics แตกต่างเล็กน้อย (ไม่ใช่ observational study)

```python
import statsmodels.api as sm
import statsmodels.formula.api as smf

# ANCOVA: outcome ~ treatment + baseline_covariates
# ปรับ baseline imbalance ผ่าน covariate adjustment

formula = f"outcome ~ treatment + {' + '.join(confounders)}"
model_ancova = smf.ols(formula, data=df).fit()

print(model_ancova.summary())

# Treatment effect (adjusted)
treatment_coef = model_ancova.params['treatment']
treatment_ci   = model_ancova.conf_int().loc['treatment']
treatment_p    = model_ancova.pvalues['treatment']

print(f"\nAdjusted Treatment Effect:")
print(f"  β = {treatment_coef:.3f} "
      f"(95% CI {treatment_ci[0]:.3f}–{treatment_ci[1]:.3f}), "
      f"p = {treatment_p:.3f}")

# สำหรับ binary outcome → Logistic ANCOVA
model_log_ancova = smf.logit(formula, data=df).fit()
OR = np.exp(model_log_ancova.params['treatment'])
OR_ci = np.exp(model_log_ancova.conf_int().loc['treatment'])
print(f"\nAdjusted OR = {OR:.3f} (95% CI {OR_ci[0]:.3f}–{OR_ci[1]:.3f})")
```

---

## 7. Manuscript Reporting Template

### Methods Section

```
We used [Propensity Score Matching / IPTW / Entropy Balancing] to account for
baseline differences between groups. Propensity scores were estimated using
logistic regression, including [list of confounders] as covariates.
[For PSM: A 1:1 nearest-neighbor matching algorithm with a caliper of 0.2 SD
of the logit-transformed propensity score was applied.]
[For IPTW: Stabilized inverse probability of treatment weights were calculated
and trimmed at the 1st and 99th percentiles to reduce the influence of extreme weights.]
Covariate balance was assessed using standardized mean differences (SMD),
with SMD < 0.1 considered indicative of adequate balance.
```

### Results Section

```
Before [matching/weighting], significant differences were observed between
the two groups in [list variables] (all SMD > 0.1, Table 1).
After [matching/weighting], baseline characteristics were well-balanced
(all SMD < 0.1, Table 2; Figure X, Love Plot).
[For PSM: Of [N] treated patients, [n] (XX%) were successfully matched
to a control patient within the specified caliper.]
```

### Table Template (Publication-ready)

```
Table 2. Baseline Characteristics Before and After Propensity Score Matching

Variable         | Before Matching          |        | After Matching           |
                 | Group A    | Group B    | SMD    | Group A    | Group B    | SMD
-----------------+------------+------------+--------+------------+------------+------
Age, years       | 65.2 ± 8.3 | 58.7 ± 9.1 | 0.75 ❌| 64.8 ± 8.5 | 64.5 ± 8.9 | 0.03 ✅
Male sex, n (%)  | 145 (72.5) | 89 (44.5)  | 0.59 ❌| 98 (65.3)  | 95 (63.3)  | 0.04 ✅
BMI, kg/m²       | 26.1 ± 4.2 | 28.3 ± 5.1 | 0.47 ❌| 26.5 ± 4.3 | 26.8 ± 4.6 | 0.07 ✅
Diabetes, n (%)  | 82 (41.0)  | 45 (22.5)  | 0.41 ❌| 56 (37.3)  | 53 (35.3)  | 0.04 ✅

SMD: Standardized Mean Difference (< 0.1 considered balanced ✅)
```

---

## 8. Sensitivity Analysis หลัง Balancing

```python
# ทดสอบ robustness ด้วย sensitivity analysis

# 8.1 E-value — ประเมิน unmeasured confounding
# E-value = RR + sqrt(RR * (RR - 1))
def compute_evalue(RR):
    """E-value สำหรับ Risk Ratio"""
    return RR + np.sqrt(RR * (RR - 1))

RR_observed = 1.8   # ตัวอย่าง
evalue = compute_evalue(RR_observed)
print(f"E-value = {evalue:.2f}")
print(f"ต้องการ unmeasured confounder ที่มี association ≥ {evalue:.2f} "
      f"กับทั้ง exposure และ outcome เพื่อ explain away ผลลัพธ์")

# 8.2 Rosenbaum Bounds (PSM only) — ตรวจ hidden bias
# ต้องการ R package rbounds (via rpy2) หรือ manual
# Γ = 1 (no hidden bias) → เพิ่ม Γ จนกว่า p > 0.05

# 8.3 วิเคราะห์ซ้ำด้วย method อื่น (triangulation)
# ถ้า PSM, IPTW, ANCOVA ให้ผลคล้ายกัน → ผลลัพธ์น่าเชื่อถือ
```

---

## 9. Package Requirements

```bash
pip install scikit-learn scipy numpy pandas matplotlib statsmodels lifelines --break-system-packages
# Optional (entropy balancing)
# pip install ebal --break-system-packages
```

---

## References

1. Austin PC. An introduction to propensity score methods for reducing the effects of confounding in observational studies. *Multivariate Behav Res.* 2011;46(3):399-424.
2. Hainmueller J. Entropy balancing for causal effects. *Polit Anal.* 2012;20(1):25-46.
3. Iacus SM, King G, Porro G. Causal inference without balance checking: Coarsened exact matching. *Polit Anal.* 2012;20(1):1-24.
4. VanderWeele TJ, Ding P. Sensitivity analysis in observational research: introducing the E-value. *Ann Intern Med.* 2017;167(4):268-274.
5. Stuart EA. Matching methods for causal inference: A review and a look forward. *Stat Sci.* 2010;25(1):1-21.
