# Inferential Statistics — Medical Research Reference
# สถิติเชิงอนุมานสำหรับงานวิจัยทางการแพทย์

## 1. แผนผังเลือกวิธีทดสอบ

```
ตัวแปร outcome เป็น continuous?
├── YES → กลุ่มเปรียบเทียบกี่กลุ่ม?
│   ├── 2 กลุ่ม → ตรวจ normality
│   │   ├── Normal → t-test (independent) หรือ paired t-test
│   │   └── Non-normal → Mann-Whitney U
│   └── ≥ 3 กลุ่ม → ตรวจ normality
│       ├── Normal → One-way ANOVA → post-hoc (Tukey/Bonferroni)
│       └── Non-normal → Kruskal-Wallis → post-hoc (Dunn)
└── NO (categorical) → ตรวจ cell frequencies
    ├── Expected ≥ 5 ทุก cell → Chi-square test
    └── Expected < 5 → Fisher's exact test
```

## 2. Parametric Tests

### Independent Samples t-test
```python
from scipy import stats
import pingouin as pg

# Basic
t_stat, p_value = stats.ttest_ind(group1, group2, equal_var=True)

# Full output with effect size
result = pg.ttest(group1, group2, paired=False, correction='auto')
print(result)
# Output: t, dof, alternative, p-val, CI95%, cohen-d, BF10, power

# สำหรับ unequal variance (Welch's t-test)
t_stat, p_value = stats.ttest_ind(group1, group2, equal_var=False)
```

**Reporting:** t([df]) = [t], p = [p], Cohen's d = [d] (95% CI [lower]–[upper])

### Paired t-test
```python
result = pg.ttest(pre, post, paired=True)
```
**ใช้เมื่อ:** before-after, matched pairs

### One-way ANOVA
```python
# Parametric
f_stat, p_value = stats.f_oneway(group1, group2, group3)

# Post-hoc (Tukey HSD)
from statsmodels.stats.multicomp import pairwise_tukeyhsd
posthoc = pairwise_tukeyhsd(df['value'], df['group'])
print(posthoc)

# Effect size: η² (eta squared)
# η² = SS_between / SS_total
```

### Repeated Measures ANOVA
```python
result = pg.rm_anova(data=df, dv='value', within='time', subject='id')
# Post-hoc
posthoc = pg.pairwise_tests(data=df, dv='value', within='time', subject='id',
                             padjust='bonferroni')
```

## 3. Non-Parametric Tests

### Mann-Whitney U (Wilcoxon Rank-Sum)
```python
stat, p = stats.mannwhitneyu(group1, group2, alternative='two-sided')
# Effect size: r = Z / sqrt(N)
import scipy
z = scipy.stats.norm.ppf(1 - p/2)
r = z / np.sqrt(len(group1) + len(group2))
```

### Kruskal-Wallis
```python
stat, p = stats.kruskal(group1, group2, group3)

# Post-hoc: Dunn test
import scikit_posthocs as sp
posthoc = sp.posthoc_dunn(df, val_col='value', group_col='group', p_adjust='bonferroni')
```

### Wilcoxon Signed-Rank (paired, non-normal)
```python
stat, p = stats.wilcoxon(pre, post)
```

## 4. Categorical Analysis

### Chi-square Test
```python
from scipy.stats import chi2_contingency

ct = pd.crosstab(df['group'], df['outcome'])
chi2, p, dof, expected = chi2_contingency(ct, correction=False)

# Yates correction สำหรับ 2×2
chi2_corrected, p_corrected, _, _ = chi2_contingency(ct, correction=True)

# Effect size: Cramér's V
V = np.sqrt(chi2 / (len(df) * (min(ct.shape) - 1)))
```

### Fisher's Exact Test
```python
odds_ratio, p_value = stats.fisher_exact(ct_2x2)
```
**ใช้เมื่อ:** n < 20, หรือ expected frequency < 5 ใน > 20% of cells

## 5. Regression Analysis

### Simple Logistic Regression
```python
import statsmodels.api as sm

X = sm.add_constant(df['predictor'])
model = sm.Logit(df['outcome'], X).fit()

# OR + 95% CI
OR = np.exp(model.params)
CI = np.exp(model.conf_int())
p = model.pvalues

results_df = pd.DataFrame({
    'OR': OR,
    'CI_lower': CI[0],
    'CI_upper': CI[1],
    'p_value': p
})
```

### Multiple Logistic Regression
```python
# 1. Variable selection: อย่า p-value based เพราะ biased
# ใช้: univariable OR, clinical importance, backward elimination

# 2. Check multicollinearity
from statsmodels.stats.outliers_influence import variance_inflation_factor
vif = pd.DataFrame()
vif['Variable'] = X.columns
vif['VIF'] = [variance_inflation_factor(X.values, i) for i in range(X.shape[1])]
# VIF > 10 = multicollinearity problem

# 3. Model fit
print(f"Pseudo R² (McFadden): {model.prsquared:.3f}")
print(f"Hosmer-Lemeshow: use statsmodels")

# Hosmer-Lemeshow test
import statsmodels.stats.diagnostic as diag
hl = diag.HosmerLemeshowTest(model.predict(), df['outcome'])
```

### Linear Regression
```python
model = sm.OLS(df['outcome'], sm.add_constant(df[predictors])).fit()
print(model.summary())

# Diagnostics
import matplotlib.pyplot as plt
fig, axes = plt.subplots(2, 2, figsize=(12, 8))
# QQ plot, residual plot, scale-location, leverage
sm.graphics.plot_regress_exog(model, 'predictor', fig=fig)
```

## 6. Correlation

```python
# Pearson (continuous, normal)
r, p = stats.pearsonr(x, y)

# Spearman (non-normal, ordinal)
rho, p = stats.spearmanr(x, y)

# Interpretation (Cohen's guideline):
# |r| < 0.1: negligible
# 0.1–0.3: small
# 0.3–0.5: moderate
# > 0.5: large
```

## 7. Multiple Testing Correction

```python
from statsmodels.stats.multitest import multipletests

p_values = [0.01, 0.04, 0.03, 0.001, 0.5]

# Bonferroni
reject, p_bonferroni, _, _ = multipletests(p_values, method='bonferroni')

# FDR (Benjamini-Hochberg) — แนะนำสำหรับ exploratory
reject, p_fdr, _, _ = multipletests(p_values, method='fdr_bh')
```

## 8. Sample Size Calculation

```python
import statsmodels.stats.power as smp

# Two-sample t-test
n = smp.tt_ind_solve_power(
    effect_size=0.5,    # Cohen's d
    alpha=0.05,
    power=0.80,
    ratio=1.0,          # n2/n1
    alternative='two-sided'
)
print(f"Required n per group: {np.ceil(n):.0f}")

# Chi-square test
from statsmodels.stats.proportion import proportion_effectsize
es = proportion_effectsize(p1, p2)
n = smp.GofChisquarePower().solve_power(
    effect_size=es, alpha=0.05, power=0.80, n_bins=2
)

# Logistic regression
from pypi import pwr  # R-like
# หรือใช้ G*Power software reference
```

## 9. Results Table Template (Publication-ready)

```
Table 2. Multivariable Logistic Regression Analysis

Variable              | β      | SE    | Wald χ² | p      | OR    | 95% CI
----------------------|--------|-------|---------|--------|-------|------------------
Age (per year)        | 0.032  | 0.012 | 7.11    | 0.008  | 1.03  | 1.01–1.06
Male sex              | 0.45   | 0.18  | 6.25    | 0.012  | 1.57  | 1.10–2.24
Diabetes mellitus     | 0.89   | 0.22  | 16.40   | <0.001 | 2.43  | 1.58–3.73
BMI (per kg/m²)       | 0.04   | 0.02  | 4.00    | 0.046  | 1.04  | 1.001–1.08

Hosmer-Lemeshow: χ²(8) = 6.24, p = 0.62
Nagelkerke R² = 0.18
Area under ROC curve = 0.74 (95% CI 0.68–0.80)
```
