# STATA-Style Figure Guide for Medical Research
# คู่มือสร้าง Figure สไตล์ STATA ด้วย Python (Matplotlib)

> **หลักการ**: ทุก figure ที่สร้างจาก plugin นี้ต้องใช้ STATA-style เป็นค่าเริ่มต้น
> STATA style เป็นมาตรฐานที่นิยมในการตีพิมพ์งานวิจัยทางการแพทย์ระดับสากล

---

## STATA Color Palette (s2color scheme)

สีมาตรฐานของ STATA s2color scheme ที่ใช้ใน medical literature:

```python
STATA_COLORS = {
    'navy':          '#1a476f',   # สีหลัก — group 1, primary line
    'maroon':        '#90353b',   # สีรอง — group 2, comparison
    'forest_green':  '#55752f',   # กลุ่มที่ 3
    'dull_teal':     '#4a8099',   # กลุ่มที่ 4
    'cranberry':     '#8b0d55',   # highlight / significant
    'khaki':         '#c8b535',   # กลุ่มที่ 6
    'sienna':        '#95612a',   # กลุ่มที่ 7
    'emidblue':      '#6e7b8b',   # neutral / reference line
    'gray':          '#808080',   # เส้น reference, censoring marks
    'black':         '#000000',   # axis, text
}

# Ordered palette สำหรับใช้งานตามลำดับกลุ่ม
STATA_PALETTE = [
    '#1a476f',  # navy
    '#90353b',  # maroon
    '#55752f',  # forest_green
    '#4a8099',  # dull_teal
    '#8b0d55',  # cranberry
    '#c8b535',  # khaki
    '#95612a',  # sienna
]
```

---

## Base STATA Theme Function

ใส่ไว้ต้น script **ทุกครั้ง** ก่อนสร้าง figure:

```python
import matplotlib.pyplot as plt
import matplotlib as mpl
import numpy as np

def set_stata_style():
    """
    ตั้งค่า matplotlib ให้มีสไตล์คล้าย STATA s2color scheme
    เรียกฟังก์ชันนี้ก่อนสร้าง figure ทุกครั้ง
    """
    plt.rcParams.update({
        # --- Font ---
        'font.family':        'sans-serif',
        'font.sans-serif':    ['Arial', 'Helvetica', 'DejaVu Sans'],
        'font.size':          11,
        'axes.titlesize':     12,
        'axes.labelsize':     11,
        'xtick.labelsize':    10,
        'ytick.labelsize':    10,
        'legend.fontsize':    10,

        # --- Figure ---
        'figure.facecolor':   'white',
        'figure.dpi':         100,
        'savefig.dpi':        300,
        'savefig.facecolor':  'white',
        'savefig.bbox':       'tight',

        # --- Axes ---
        'axes.facecolor':     'white',
        'axes.edgecolor':     '#000000',
        'axes.linewidth':     0.8,
        'axes.spines.top':    False,     # ไม่มีเส้นกรอบบน (STATA style)
        'axes.spines.right':  False,     # ไม่มีเส้นกรอบขวา (STATA style)
        'axes.grid':          True,
        'axes.axisbelow':     True,

        # --- Grid ---
        'grid.color':         '#e0e0e0',
        'grid.linewidth':     0.5,
        'grid.linestyle':     '-',
        'grid.alpha':         0.7,

        # --- Ticks ---
        'xtick.direction':    'out',     # tick ชี้ออก (STATA style)
        'ytick.direction':    'out',
        'xtick.major.size':   4,
        'ytick.major.size':   4,
        'xtick.minor.size':   2,
        'ytick.minor.size':   2,
        'xtick.color':        '#000000',
        'ytick.color':        '#000000',

        # --- Lines ---
        'lines.linewidth':    1.5,
        'lines.markersize':   6,

        # --- Legend ---
        'legend.frameon':         True,
        'legend.framealpha':      0.9,
        'legend.edgecolor':       '#cccccc',
        'legend.fancybox':        False,
        'legend.shadow':          False,

        # --- Error bars ---
        'errorbar.capsize':       4,
    })

# เรียกใช้ที่ต้น script
set_stata_style()

STATA_PALETTE = ['#1a476f','#90353b','#55752f','#4a8099','#8b0d55','#c8b535','#95612a']
```

---

## Figure Templates ตาม STATA Style

### 1. Kaplan-Meier Survival Curve

```python
from lifelines import KaplanMeierFitter
from lifelines.statistics import logrank_test
import matplotlib.pyplot as plt
import matplotlib.patches as mpatches

def plot_km_stata(df, time_col, event_col, group_col,
                  group_labels=None, time_unit='months',
                  title='Kaplan-Meier Survival Curve',
                  ylabel='Survival Probability',
                  show_at_risk=True,
                  save_path=None):
    """
    สร้าง Kaplan-Meier curve สไตล์ STATA
    """
    set_stata_style()

    groups = sorted(df[group_col].unique())
    if group_labels is None:
        group_labels = {g: str(g) for g in groups}

    # สร้าง figure พร้อม at-risk table (ถ้าต้องการ)
    if show_at_risk:
        fig, (ax_main, ax_risk) = plt.subplots(
            2, 1, figsize=(10, 8),
            gridspec_kw={'height_ratios': [4, 1], 'hspace': 0.05}
        )
    else:
        fig, ax_main = plt.subplots(figsize=(10, 6))

    kmf_list = []

    for i, group in enumerate(groups):
        mask = df[group_col] == group
        kmf = KaplanMeierFitter()
        kmf.fit(
            df.loc[mask, time_col],
            event_observed=df.loc[mask, event_col],
            label=group_labels[group]
        )
        kmf_list.append(kmf)

        # Plot survival curve — STATA style: step function + CI shading
        color = STATA_PALETTE[i % len(STATA_PALETTE)]
        kmf.plot_survival_function(
            ax=ax_main,
            color=color,
            linewidth=1.8,
            ci_alpha=0.12,          # transparent CI band (STATA-like)
            ci_show=True,
            at_risk_counts=False
        )

        # Censoring marks — vertical tick (STATA style)
        event_table = kmf.event_table
        censored_times = event_table[event_table['censored'] > 0].index
        for t in censored_times:
            sf_at_t = kmf.survival_function_at_times(t).values[0]
            ax_main.plot(t, sf_at_t, '|',
                        color=color, markersize=6, markeredgewidth=1.2)

    # Log-rank test (2 กลุ่ม)
    if len(groups) == 2:
        lr = logrank_test(
            df[df[group_col]==groups[0]][time_col],
            df[df[group_col]==groups[1]][time_col],
            event_observed_A=df[df[group_col]==groups[0]][event_col],
            event_observed_B=df[df[group_col]==groups[1]][event_col]
        )
        p_val = lr.p_value
        p_text = f'p < 0.001' if p_val < 0.001 else f'p = {p_val:.3f}'

        # วาง p-value ใน upper right (STATA convention)
        ax_main.text(0.97, 0.92, f'Log-rank test\n{p_text}',
                    transform=ax_main.transAxes,
                    ha='right', va='top', fontsize=10,
                    bbox=dict(boxstyle='square,pad=0.4',
                             facecolor='white', edgecolor='#cccccc', linewidth=0.8))

    # Axes formatting — STATA style
    ax_main.set_ylim(-0.02, 1.05)
    ax_main.set_xlim(left=0)
    ax_main.set_ylabel(ylabel, fontsize=11)
    ax_main.set_title(title, fontsize=12, fontweight='normal', pad=10)

    if not show_at_risk:
        ax_main.set_xlabel(f'Time ({time_unit})', fontsize=11)

    # Legend — ใน plot (STATA default)
    ax_main.legend(loc='upper right', framealpha=0.9,
                  edgecolor='#cccccc', fontsize=10)

    # At-risk table (STATA landmark feature)
    if show_at_risk:
        ax_main.set_xticklabels([])
        ax_main.tick_params(axis='x', bottom=False)

        time_points = np.linspace(0, df[time_col].max(), 6).astype(int)

        for i, (kmf, group) in enumerate(zip(kmf_list, groups)):
            color = STATA_PALETTE[i % len(STATA_PALETTE)]
            at_risk = [kmf.event_table.loc[
                kmf.event_table.index[kmf.event_table.index <= t].max()
                if any(kmf.event_table.index <= t) else 0, 'at_risk'
            ] if t > 0 else kmf.event_table.iloc[0]['at_risk']
                      for t in time_points]

            for j, (t, n) in enumerate(zip(time_points, at_risk)):
                ax_risk.text(t, len(groups) - i - 0.5, str(int(n)),
                           ha='center', va='center', fontsize=9, color=color)

        ax_risk.set_xlim(ax_main.get_xlim())
        ax_risk.set_ylim(0, len(groups))
        ax_risk.set_yticks([len(groups) - i - 0.5 for i in range(len(groups))])
        ax_risk.set_yticklabels([group_labels[g] for g in groups],
                               fontsize=9, ha='right')
        ax_risk.set_xlabel(f'Time ({time_unit})', fontsize=11)
        ax_risk.spines['top'].set_visible(False)
        ax_risk.spines['right'].set_visible(False)
        ax_risk.spines['left'].set_visible(False)
        ax_risk.grid(False)
        ax_risk.tick_params(axis='y', length=0)
        ax_risk.text(-0.08, 0.5, 'At risk', transform=ax_risk.transAxes,
                    ha='right', va='center', fontsize=9, style='italic')

    plt.tight_layout()

    if save_path:
        plt.savefig(save_path, dpi=300, bbox_inches='tight', facecolor='white')
    return fig, ax_main
```

---

### 2. ROC Curve

```python
from sklearn.metrics import roc_curve, roc_auc_score
from scipy import stats as scipy_stats

def plot_roc_stata(y_true, y_scores, model_labels=None,
                  title='ROC Curve',
                  save_path=None):
    """
    สร้าง ROC curve สไตล์ STATA
    รองรับหลาย model (y_scores เป็น list of arrays)
    """
    set_stata_style()

    # รองรับ single หรือ multiple models
    if not isinstance(y_scores, list):
        y_scores = [y_scores]
    if model_labels is None:
        model_labels = [f'Model {i+1}' for i in range(len(y_scores))]

    fig, ax = plt.subplots(figsize=(7, 7))

    for i, (y_score, label) in enumerate(zip(y_scores, model_labels)):
        fpr, tpr, thresholds = roc_curve(y_true, y_score)
        auc = roc_auc_score(y_true, y_score)

        # 95% CI for AUC (DeLong method approximation)
        n1 = sum(y_true == 1)
        n0 = sum(y_true == 0)
        q1 = auc / (2 - auc)
        q2 = 2 * auc**2 / (1 + auc)
        se = np.sqrt((auc*(1-auc) + (n1-1)*(q1-auc**2) + (n0-1)*(q2-auc**2)) / (n1*n0))
        ci_lower = max(0, auc - 1.96*se)
        ci_upper = min(1, auc + 1.96*se)

        color = STATA_PALETTE[i % len(STATA_PALETTE)]

        # ROC curve
        ax.plot(fpr, tpr,
               color=color, linewidth=2,
               label=f'{label}\nAUC = {auc:.3f} (95% CI {ci_lower:.3f}–{ci_upper:.3f})')

        # Optimal cutpoint (Youden's index) — STATA marks this point
        youden = tpr - fpr
        opt_idx = np.argmax(youden)
        ax.plot(fpr[opt_idx], tpr[opt_idx], 'o',
               color=color, markersize=7, zorder=5,
               markeredgecolor='white', markeredgewidth=1.5)

    # Diagonal reference line — STATA style (dashed gray)
    ax.plot([0, 1], [0, 1], '--', color='#808080', linewidth=1, zorder=1,
           label='Reference (AUC = 0.500)')

    # Axes formatting
    ax.set_xlim(-0.02, 1.02)
    ax.set_ylim(-0.02, 1.05)
    ax.set_xlabel('1 − Specificity (False Positive Rate)', fontsize=11)
    ax.set_ylabel('Sensitivity (True Positive Rate)', fontsize=11)
    ax.set_title(title, fontsize=12, fontweight='normal', pad=10)

    # Legend — lower right (STATA convention for ROC)
    ax.legend(loc='lower right', fontsize=9.5,
             framealpha=0.95, edgecolor='#cccccc')

    # Square plot (STATA ROC is always square)
    ax.set_aspect('equal')

    plt.tight_layout()

    if save_path:
        plt.savefig(save_path, dpi=300, bbox_inches='tight', facecolor='white')
    return fig, ax
```

---

### 3. Forest Plot

```python
def plot_forest_stata(estimates, ci_lower, ci_upper, labels,
                      subgroup_indices=None, subgroup_labels=None,
                      overall_estimate=None, overall_ci=None,
                      null_value=1.0, x_label='Odds Ratio (95% CI)',
                      title='Forest Plot',
                      save_path=None):
    """
    สร้าง Forest plot สไตล์ STATA
    estimates, ci_lower, ci_upper: arrays of values
    labels: list of study/variable names
    subgroup_indices: dict {subgroup_name: [indices]} สำหรับแสดง subgroup headers
    """
    set_stata_style()

    n = len(estimates)
    y_pos = np.arange(n, 0, -1)

    # คำนวณ marker size ตาม weight (inverse variance)
    weights = 1 / ((np.array(ci_upper) - np.array(ci_lower)) / (2 * 1.96))**2
    weights_norm = (weights / weights.max()) * 12 + 3  # scale 3–15

    fig_height = max(6, n * 0.45 + 2)
    fig, ax = plt.subplots(figsize=(10, fig_height))

    navy = STATA_COLORS['navy']
    maroon = STATA_COLORS['maroon']

    # Plot CI lines และ point estimates
    for i, (est, lo, hi, y) in enumerate(zip(estimates, ci_lower, ci_upper, y_pos)):
        if np.isnan(est):  # subgroup header row
            continue

        # CI horizontal line — STATA navy
        ax.plot([lo, hi], [y, y], '-', color=navy, linewidth=1.2, zorder=2)
        # Caps
        ax.plot([lo, lo], [y-0.08, y+0.08], '-', color=navy, linewidth=1.2, zorder=2)
        ax.plot([hi, hi], [y-0.08, y+0.08], '-', color=navy, linewidth=1.2, zorder=2)
        # Point estimate (filled square — STATA meta style)
        ax.plot(est, y, 's', color=navy,
               markersize=weights_norm[i], zorder=3,
               markeredgecolor='white', markeredgewidth=0.5)

    # Overall estimate (diamond — STATA meta-analysis standard)
    if overall_estimate is not None:
        y_overall = 0
        diamond_x = [overall_ci[0], overall_estimate,
                     overall_ci[1], overall_estimate, overall_ci[0]]
        diamond_y = [y_overall, y_overall+0.25,
                     y_overall, y_overall-0.25, y_overall]
        ax.fill(diamond_x, diamond_y, color=maroon, zorder=4, alpha=0.9)
        y_positions_all = list(y_pos) + [y_overall]
    else:
        y_positions_all = list(y_pos)

    # Null value reference line — dashed gray (STATA convention)
    y_min_plot = min(y_positions_all) - 0.5
    y_max_plot = max(y_positions_all) + 0.5
    ax.axvline(x=null_value, color='#808080', linestyle='--',
              linewidth=0.8, zorder=1)

    # Y-axis labels — left aligned (STATA style)
    ax.set_yticks(y_pos)
    ax.set_yticklabels(labels, fontsize=10, ha='right')

    # Subgroup headers (bold, indented)
    if subgroup_indices:
        for sg_name, indices in subgroup_indices.items():
            y_header = max([y_pos[i] for i in indices]) + 0.7
            ax.text(ax.get_xlim()[0] if ax.get_xlim()[0] < 0 else 0,
                   y_header, sg_name,
                   fontsize=10, fontweight='bold', ha='left', va='center')

    ax.set_ylim(y_min_plot, y_max_plot)
    ax.set_xlabel(x_label, fontsize=11)
    ax.set_title(title, fontsize=12, fontweight='normal', pad=10)

    # Grid on x-axis only (STATA forest plot style)
    ax.grid(axis='x', color='#e0e0e0', linewidth=0.5)
    ax.grid(axis='y', visible=False)

    plt.tight_layout()

    if save_path:
        plt.savefig(save_path, dpi=300, bbox_inches='tight', facecolor='white')
    return fig, ax
```

---

### 4. Bar Chart

```python
def plot_bar_stata(categories, values, errors=None,
                  groups=None, group_labels=None,
                  ylabel='', title='',
                  horizontal=False,
                  save_path=None):
    """
    สร้าง Bar chart สไตล์ STATA
    รองรับ single group และ grouped bars
    """
    set_stata_style()

    fig, ax = plt.subplots(figsize=(9, 6))

    x = np.arange(len(categories))
    n_groups = len(groups) if groups is not None else 1

    if n_groups == 1 or groups is None:
        color = STATA_PALETTE[0]
        bars = ax.bar(x, values, color=color,
                     edgecolor='white', linewidth=0.5,
                     width=0.6, zorder=2)
        if errors is not None:
            ax.errorbar(x, values, yerr=errors,
                       fmt='none', color='#404040',
                       capsize=4, linewidth=1.2, zorder=3)
    else:
        width = 0.8 / n_groups
        for i, (grp_vals, grp_label) in enumerate(zip(groups, group_labels)):
            offset = (i - (n_groups-1)/2) * width
            color = STATA_PALETTE[i % len(STATA_PALETTE)]
            ax.bar(x + offset, grp_vals, width=width,
                  color=color, edgecolor='white', linewidth=0.5,
                  label=grp_label, zorder=2)

        ax.legend(loc='upper right', framealpha=0.9, edgecolor='#cccccc')

    # Axes formatting
    ax.set_xticks(x)
    # หมุน label ถ้าชื่อยาว (> 6 ตัวอักษร เฉลี่ย)
    max_len = max(len(str(c)) for c in categories)
    rotation = 45 if max_len > 6 else 0
    ha = 'right' if rotation > 0 else 'center'
    ax.set_xticklabels(categories, rotation=rotation, ha=ha)

    ax.set_ylabel(ylabel, fontsize=11)
    ax.set_title(title, fontsize=12, fontweight='normal', pad=10)
    ax.set_xlim(-0.5, len(categories) - 0.5)

    # Y starts at 0 (STATA bar default)
    ax.set_ylim(bottom=0)

    plt.tight_layout()

    if save_path:
        plt.savefig(save_path, dpi=300, bbox_inches='tight', facecolor='white')
    return fig, ax
```

---

### 5. Box Plot

```python
def plot_box_stata(df, x_col, y_col, x_labels=None,
                  ylabel='', title='',
                  add_jitter=True,
                  pvalue_pairs=None,
                  save_path=None):
    """
    สร้าง Box plot สไตล์ STATA
    pvalue_pairs: list of ((group1, group2), p_value) สำหรับใส่ significance brackets
    """
    set_stata_style()
    import matplotlib.patches as mpatches
    from itertools import combinations

    groups = sorted(df[x_col].unique())
    if x_labels is None:
        x_labels = {g: str(g) for g in groups}

    fig, ax = plt.subplots(figsize=(8, 6))

    data_by_group = [df[df[x_col] == g][y_col].dropna().values for g in groups]

    # Box plot — STATA style: no notch, whiskers at 5th-95th percentile
    bp = ax.boxplot(data_by_group,
                   patch_artist=True,
                   notch=False,
                   whis=[5, 95],       # STATA default whiskers
                   showfliers=False,   # แสดง outliers แยก
                   widths=0.5,
                   medianprops=dict(color='white', linewidth=2))

    for i, patch in enumerate(bp['boxes']):
        color = STATA_PALETTE[i % len(STATA_PALETTE)]
        patch.set_facecolor(color)
        patch.set_alpha(0.85)
        patch.set_edgecolor('#404040')
        patch.set_linewidth(0.8)
        bp['whiskers'][i*2].set_color('#404040')
        bp['whiskers'][i*2+1].set_color('#404040')
        bp['caps'][i*2].set_color('#404040')
        bp['caps'][i*2+1].set_color('#404040')

    # Jitter overlay (scatter ข้อมูลดิบ — STATA beeswarm style)
    if add_jitter:
        for i, (data, group) in enumerate(zip(data_by_group, groups)):
            color = STATA_PALETTE[i % len(STATA_PALETTE)]
            jitter = np.random.normal(0, 0.06, len(data))
            ax.scatter(np.full(len(data), i+1) + jitter, data,
                      color=color, alpha=0.35, s=12, zorder=2,
                      edgecolors='none')

    # Outliers (points beyond whiskers)
    for i, data in enumerate(data_by_group):
        color = STATA_PALETTE[i % len(STATA_PALETTE)]
        q5, q95 = np.percentile(data, [5, 95])
        outliers = data[(data < q5) | (data > q95)]
        if len(outliers) > 0:
            ax.scatter(np.full(len(outliers), i+1), outliers,
                      marker='o', color=color, s=18, zorder=3,
                      facecolors='none', edgecolors=color, linewidth=0.8)

    # P-value brackets
    if pvalue_pairs:
        y_max = max(np.percentile(d, 95) for d in data_by_group)
        y_range = ax.get_ylim()[1] - ax.get_ylim()[0]
        bracket_gap = y_range * 0.06

        for k, ((g1, g2), p_val) in enumerate(pvalue_pairs):
            i1 = groups.index(g1) + 1
            i2 = groups.index(g2) + 1
            y_bracket = y_max + bracket_gap * (k + 1.2)

            # Bracket lines
            ax.plot([i1, i1, i2, i2],
                   [y_bracket - bracket_gap*0.3, y_bracket,
                    y_bracket, y_bracket - bracket_gap*0.3],
                   color='#404040', linewidth=0.8)

            # P-value text
            if p_val < 0.001:
                p_text = 'p < 0.001'
            elif p_val < 0.01:
                p_text = f'p = {p_val:.3f}'
            else:
                p_text = f'p = {p_val:.3f}'

            ax.text((i1+i2)/2, y_bracket + bracket_gap*0.1,
                   p_text, ha='center', va='bottom', fontsize=9)

        # ขยาย y-limit ให้รองรับ brackets
        ax.set_ylim(top=y_max + bracket_gap * (len(pvalue_pairs) + 2))

    ax.set_xticks(range(1, len(groups)+1))
    max_len = max(len(x_labels[g]) for g in groups)
    rotation = 45 if max_len > 8 else 0
    ha = 'right' if rotation > 0 else 'center'
    ax.set_xticklabels([x_labels[g] for g in groups],
                      rotation=rotation, ha=ha)

    ax.set_ylabel(ylabel, fontsize=11)
    ax.set_title(title, fontsize=12, fontweight='normal', pad=10)

    plt.tight_layout()

    if save_path:
        plt.savefig(save_path, dpi=300, bbox_inches='tight', facecolor='white')
    return fig, ax
```

---

### 6. Scatter Plot with Regression Line

```python
def plot_scatter_stata(x, y, group=None, group_labels=None,
                       xlabel='', ylabel='', title='',
                       add_regression=True,
                       save_path=None):
    """
    สร้าง Scatter plot สไตล์ STATA พร้อม regression line
    """
    set_stata_style()
    from scipy import stats as sp_stats
    from adjustText import adjust_text  # pip install adjustText

    fig, ax = plt.subplots(figsize=(8, 7))

    if group is None:
        color = STATA_PALETTE[0]
        ax.scatter(x, y, color=color, s=40, alpha=0.7,
                  edgecolors='white', linewidth=0.4, zorder=3)

        if add_regression:
            slope, intercept, r, p, se = sp_stats.linregress(x, y)
            x_line = np.linspace(min(x), max(x), 100)
            y_line = slope * x_line + intercept

            ax.plot(x_line, y_line, '-', color=STATA_COLORS['maroon'],
                   linewidth=1.5, zorder=2,
                   label=f'y = {slope:.3f}x + {intercept:.3f}')

            # R² annotation (STATA รายงาน R²)
            r2_text = f'R² = {r**2:.3f}\np = {"< 0.001" if p < 0.001 else f"{p:.3f}"}'
            ax.text(0.05, 0.92, r2_text,
                   transform=ax.transAxes,
                   ha='left', va='top', fontsize=10,
                   bbox=dict(boxstyle='square,pad=0.4',
                            facecolor='white', edgecolor='#cccccc'))
    else:
        for i, grp in enumerate(sorted(set(group))):
            mask = np.array(group) == grp
            label = group_labels[grp] if group_labels else str(grp)
            color = STATA_PALETTE[i % len(STATA_PALETTE)]
            ax.scatter(np.array(x)[mask], np.array(y)[mask],
                      color=color, s=40, alpha=0.7,
                      edgecolors='white', linewidth=0.4,
                      label=label, zorder=3)

        ax.legend(loc='best', framealpha=0.9, edgecolor='#cccccc')

    ax.set_xlabel(xlabel, fontsize=11)
    ax.set_ylabel(ylabel, fontsize=11)
    ax.set_title(title, fontsize=12, fontweight='normal', pad=10)

    plt.tight_layout()

    if save_path:
        plt.savefig(save_path, dpi=300, bbox_inches='tight', facecolor='white')
    return fig, ax
```

---

## Quick Reference: สรุปการใช้งาน

| Figure Type | Function | ใช้เมื่อ |
|---|---|---|
| Kaplan-Meier | `plot_km_stata()` | Survival analysis, time-to-event |
| ROC Curve | `plot_roc_stata()` | Diagnostic accuracy, AUC comparison |
| Forest Plot | `plot_forest_stata()` | Meta-analysis, subgroup, regression coefficients |
| Bar Chart | `plot_bar_stata()` | Categorical outcomes, comparison |
| Box Plot | `plot_box_stata()` | Distribution comparison, continuous outcomes |
| Scatter Plot | `plot_scatter_stata()` | Correlation, regression, continuous relationship |

## Required Libraries

```bash
pip install matplotlib numpy scipy lifelines scikit-learn adjustText
```

## Export Standards (Publication-Ready)

```python
# บันทึกทุก figure ด้วย settings เหล่านี้:
plt.savefig('figure1_km_curve.tiff', dpi=300, bbox_inches='tight',
           facecolor='white', format='tiff')

# หรือ PNG สำหรับ submission บางวารสาร:
plt.savefig('figure1_km_curve.png', dpi=300, bbox_inches='tight',
           facecolor='white')
```
