# Survival Analysis — Medical Research Reference
# การวิเคราะห์ Survival สำหรับงานวิจัยทางการแพทย์

## 1. Kaplan-Meier Analysis

```python
from lifelines import KaplanMeierFitter
from lifelines.statistics import logrank_test, multivariate_logrank_test
import matplotlib.pyplot as plt
import matplotlib.ticker as mticker

# Setup
kmf = KaplanMeierFitter()

# Single curve
kmf.fit(df['time'], event_observed=df['event'], label='Overall')
print(f"Median OS: {kmf.median_survival_time_:.1f} months")
print(f"95% CI: {kmf.confidence_interval_.iloc[0].values}")

# Landmark survival rates
print(f"1-year OS: {kmf.survival_function_at_times([12]).values[0]:.3f}")
print(f"3-year OS: {kmf.survival_function_at_times([36]).values[0]:.3f}")
print(f"5-year OS: {kmf.survival_function_at_times([60]).values[0]:.3f}")

# Publication-quality KM plot
fig, ax = plt.subplots(figsize=(10, 7))
colors = ['#2196F3', '#F44336', '#4CAF50', '#FF9800']

for i, group in enumerate(df['group'].unique()):
    mask = df['group'] == group
    kmf.fit(df.loc[mask, 'time'],
            event_observed=df.loc[mask, 'event'],
            label=f'{group} (n={mask.sum()})')
    kmf.plot_survival_function(ax=ax, ci_show=True,
                                color=colors[i], linewidth=2)

# Log-rank test
results = logrank_test(
    df[df['group']==0]['time'], df[df['group']==1]['time'],
    event_observed_A=df[df['group']==0]['event'],
    event_observed_B=df[df['group']==1]['event']
)
ax.text(0.65, 0.85, f'Log-rank p = {results.p_value:.4f}',
        transform=ax.transAxes, fontsize=12,
        bbox=dict(boxstyle='round', facecolor='wheat', alpha=0.5))

# Formatting
ax.set_xlabel('Time (months)', fontsize=14)
ax.set_ylabel('Survival Probability', fontsize=14)
ax.set_title('Overall Survival by Treatment Group', fontsize=16)
ax.set_ylim(0, 1.05)
ax.legend(loc='upper right', fontsize=12)
ax.grid(True, alpha=0.3)

# At-risk table
from lifelines.plotting import add_at_risk_counts
add_at_risk_counts(kmf, ax=ax, fontsize=10)

plt.tight_layout()
plt.savefig('kaplan_meier.png', dpi=300, bbox_inches='tight')
```

## 2. Log-rank Test Variations

```python
# Standard log-rank
results = logrank_test(T1, T2, E1, E2)

# Weighted log-rank (Gehan/Peto — emphasizes early differences)
results_peto = logrank_test(T1, T2, E1, E2, weightings='wilcoxon')

# More than 2 groups
multivar = multivariate_logrank_test(df['time'], df['group'], df['event'])
multivar.print_summary()
# ถ้า significant → pairwise comparison with Bonferroni correction

# Stratified log-rank
from lifelines.statistics import multivariate_logrank_test
results_strat = multivariate_logrank_test(df['time'], df['group'], df['event'],
                                           strata=df['stratum'])
```

## 3. Cox Proportional Hazards Model

```python
from lifelines import CoxPHFitter
import pandas as pd

# Prepare data (ต้องมี: time, event, covariates)
df_cox = df[['time', 'event', 'treatment', 'age', 'sex', 'stage']].copy()

# Fit model
cph = CoxPHFitter(penalizer=0.1)  # L2 regularization optional
cph.fit(df_cox, duration_col='time', event_col='event')
cph.print_summary(decimals=3, model='cox')

# Extract results
summary = cph.summary
summary['HR'] = np.exp(summary['coef'])
summary['HR_lower'] = np.exp(summary['coef lower 95%'])
summary['HR_upper'] = np.exp(summary['coef upper 95%'])

# Baseline survival
cph.baseline_survival_.plot()
```

### Checking Proportional Hazards Assumption
```python
# Schoenfeld residuals
cph.check_assumptions(df_cox, show_plots=True, p_value_threshold=0.05)

# Manual: log(-log(S(t))) plot — เส้นขนานกัน = PH satisfied
for group in df['group'].unique():
    mask = df['group'] == group
    kmf.fit(df.loc[mask, 'time'], event_observed=df.loc[mask, 'event'])
    log_log_s = np.log(-np.log(kmf.survival_function_))
    plt.plot(np.log(kmf.survival_function_.index), log_log_s.values.flatten(),
             label=f'Group {group}')

# ถ้า PH violated: use time-varying coefficients หรือ stratified Cox
cph_tv = CoxTimeVaryingFitter()
```

### Stratified Cox Model
```python
cph_strat = CoxPHFitter()
cph_strat.fit(df_cox, duration_col='time', event_col='event',
              strata=['center'])  # stratify by center/stage
```

## 4. Competing Risks Analysis

เมื่อมี competing events (เช่น death from other causes)

```python
# Fine and Gray subdistribution hazard
# ใช้ R::cmprsk หรือ Python lifelines (ยังไม่ครบ)
# แนะนำ: รัน R script ผ่าน subprocess หรือ rpy2

import subprocess
r_script = """
library(cmprsk)
# Fine-Gray model
fg_model <- crr(ftime, fstatus, cov1, failcode=1, cencode=0)
summary(fg_model)
"""
# หรือใช้ lifelines สำหรับ cause-specific hazard
from lifelines import CoxPHFitter
# Separate models for each cause
```

## 5. Landmark Analysis

สำหรับ time-varying treatment หรือ immortal time bias

```python
def landmark_analysis(df, landmark_time, time_col, event_col, group_col):
    """
    วิเคราะห์เฉพาะผู้ป่วยที่รอดถึง landmark time
    """
    # เลือกเฉพาะผู้ป่วยที่รอดถึง landmark
    df_landmark = df[df[time_col] > landmark_time].copy()

    # ปรับ time
    df_landmark[time_col] = df_landmark[time_col] - landmark_time

    # วิเคราะห์ตามปกติ
    for group in df_landmark[group_col].unique():
        mask = df_landmark[group_col] == group
        kmf.fit(df_landmark.loc[mask, time_col],
                event_observed=df_landmark.loc[mask, event_col],
                label=f'{group} (from {landmark_time}m)')
        kmf.plot_survival_function()

    return df_landmark
```

## 6. Results Reporting Template

### Methods Section
```
Survival curves were estimated using the Kaplan-Meier method and compared
with the log-rank test. Hazard ratios (HRs) with 95% confidence intervals (CIs)
were estimated using Cox proportional hazards regression. The proportional
hazards assumption was verified using Schoenfeld residuals and log-log plots.
Multivariable models were adjusted for [covariates]. All analyses were two-sided,
with a significance threshold of p < 0.05. Statistical analysis was performed
using Python (lifelines v0.27, statsmodels v0.14).
```

### Results Section Template
```
Median overall survival was X.X months (95% CI Y.Y–Z.Z) in the treatment group
vs. X.X months (95% CI Y.Y–Z.Z) in the control group (log-rank p = 0.xxx).
The 1-, 3-, and 5-year OS rates were X%, X%, and X% in the treatment group
vs. X%, X%, and X% in the control group, respectively.

On multivariable Cox regression, treatment was independently associated with
improved OS (HR 0.XX, 95% CI 0.XX–0.XX, p = 0.xxx), after adjusting for
age, sex, and disease stage.
```

## 7. Forest Plot สำหรับ Subgroup Analysis

```python
import matplotlib.pyplot as plt
import numpy as np

def forest_plot(subgroups, hrs, ci_lower, ci_upper, p_values):
    fig, ax = plt.subplots(figsize=(10, len(subgroups) * 0.8 + 2))

    y_positions = range(len(subgroups))

    for i, (group, hr, lower, upper, p) in enumerate(
            zip(subgroups, hrs, ci_lower, ci_upper, p_values)):
        ax.plot([lower, upper], [i, i], 'b-', linewidth=2)
        ax.plot(hr, i, 'bs', markersize=8)
        ax.text(max(ci_upper)*1.1, i, f'{hr:.2f} ({lower:.2f}–{upper:.2f})',
                va='center', fontsize=10)

    ax.axvline(x=1, color='gray', linestyle='--', linewidth=1)
    ax.set_yticks(y_positions)
    ax.set_yticklabels(subgroups)
    ax.set_xscale('log')
    ax.set_xlabel('Hazard Ratio (log scale)')
    ax.set_title('Subgroup Analysis — Overall Survival')
    plt.tight_layout()
    plt.savefig('forest_plot.png', dpi=300)
```
