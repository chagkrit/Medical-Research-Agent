# Diagnostic Accuracy Analysis — STARD Compliant
# การวิเคราะห์ความแม่นยำในการวินิจฉัย

## 1. Core Metrics

```python
import numpy as np
import pandas as pd
from sklearn.metrics import (roc_auc_score, roc_curve, confusion_matrix,
                              average_precision_score, precision_recall_curve)
import matplotlib.pyplot as plt
from scipy import stats

def diagnostic_accuracy_full(y_true, y_score, test_name="Test"):
    """
    วิเคราะห์ diagnostic accuracy ครบสมบูรณ์ตาม STARD 2015
    """
    # ROC
    fpr, tpr, thresholds = roc_curve(y_true, y_score)
    auc = roc_auc_score(y_true, y_score)

    # Youden's Index — optimal cutpoint
    youden = tpr - fpr
    optimal_idx = np.argmax(youden)
    optimal_threshold = thresholds[optimal_idx]

    # Confusion matrix at optimal cutpoint
    y_pred = (y_score >= optimal_threshold).astype(int)
    tn, fp, fn, tp = confusion_matrix(y_true, y_pred).ravel()
    n = len(y_true)

    # Core metrics
    sensitivity = tp / (tp + fn)
    specificity = tn / (tn + fp)
    ppv = tp / (tp + fp) if (tp + fp) > 0 else 0
    npv = tn / (tn + fn) if (tn + fn) > 0 else 0
    plr = sensitivity / (1 - specificity) if (1-specificity) > 0 else np.inf
    nlr = (1 - sensitivity) / specificity if specificity > 0 else np.inf
    dor = plr / nlr if nlr > 0 else np.inf
    accuracy = (tp + tn) / n

    # 95% CIs using Wilson score interval
    def wilson_ci(p, n, z=1.96):
        denominator = 1 + z**2/n
        center = (p + z**2/(2*n)) / denominator
        margin = (z * np.sqrt(p*(1-p)/n + z**2/(4*n**2))) / denominator
        return max(0, center - margin), min(1, center + margin)

    sens_ci = wilson_ci(sensitivity, tp + fn)
    spec_ci = wilson_ci(specificity, tn + fp)
    ppv_ci = wilson_ci(ppv, tp + fp)
    npv_ci = wilson_ci(npv, tn + fn)

    # AUC 95% CI (DeLong method via bootstrapping)
    n_boot = 1000
    auc_boots = []
    for _ in range(n_boot):
        idx = np.random.choice(len(y_true), len(y_true), replace=True)
        try:
            auc_boots.append(roc_auc_score(y_true[idx], y_score[idx]))
        except:
            pass
    auc_ci = (np.percentile(auc_boots, 2.5), np.percentile(auc_boots, 97.5))

    # Print results
    print(f"\n{'='*60}")
    print(f"DIAGNOSTIC ACCURACY REPORT: {test_name}")
    print(f"{'='*60}")
    print(f"Optimal cutpoint: {optimal_threshold:.4f} (Youden index = {youden[optimal_idx]:.4f})")
    print(f"\n2×2 Contingency Table:")
    print(f"  TP = {tp}  FP = {fp}")
    print(f"  FN = {fn}  TN = {tn}")
    print(f"\nCore Metrics (95% CI):")
    print(f"  AUC:         {auc:.3f} ({auc_ci[0]:.3f}–{auc_ci[1]:.3f})")
    print(f"  Sensitivity: {sensitivity:.3f} ({sens_ci[0]:.3f}–{sens_ci[1]:.3f})")
    print(f"  Specificity: {specificity:.3f} ({spec_ci[0]:.3f}–{spec_ci[1]:.3f})")
    print(f"  PPV:         {ppv:.3f} ({ppv_ci[0]:.3f}–{ppv_ci[1]:.3f})")
    print(f"  NPV:         {npv:.3f} ({npv_ci[0]:.3f}–{npv_ci[1]:.3f})")
    print(f"  +LR:         {plr:.2f}")
    print(f"  -LR:         {nlr:.3f}")
    print(f"  DOR:         {dor:.1f}")
    print(f"  Accuracy:    {accuracy:.3f}")

    return {
        'auc': auc, 'auc_ci': auc_ci,
        'sensitivity': sensitivity, 'sens_ci': sens_ci,
        'specificity': specificity, 'spec_ci': spec_ci,
        'ppv': ppv, 'ppv_ci': ppv_ci,
        'npv': npv, 'npv_ci': npv_ci,
        'plr': plr, 'nlr': nlr, 'dor': dor,
        'optimal_threshold': optimal_threshold,
        'fpr': fpr, 'tpr': tpr, 'thresholds': thresholds
    }
```

## 2. ROC Curve — Publication Quality

```python
def plot_roc_comparison(models_dict, title="ROC Curves"):
    """
    models_dict = {'Model A': (y_true, y_score), 'Model B': (y_true, y_score)}
    """
    fig, ax = plt.subplots(figsize=(8, 7))
    colors = ['#2196F3', '#F44336', '#4CAF50', '#FF9800', '#9C27B0']

    for (name, (y_true, y_score)), color in zip(models_dict.items(), colors):
        fpr, tpr, _ = roc_curve(y_true, y_score)
        auc = roc_auc_score(y_true, y_score)

        # Bootstrap CI
        aucs = [roc_auc_score(y_true[np.random.choice(len(y_true), len(y_true), replace=True)],
                               y_score[np.random.choice(len(y_score), len(y_score), replace=True)])
                for _ in range(1000)]
        ci = (np.percentile(aucs, 2.5), np.percentile(aucs, 97.5))

        ax.plot(fpr, tpr, color=color, linewidth=2.5,
                label=f'{name}\nAUC = {auc:.3f} (95% CI {ci[0]:.3f}–{ci[1]:.3f})')

    # Reference line
    ax.plot([0, 1], [0, 1], 'k--', linewidth=1, alpha=0.5, label='Reference (AUC = 0.5)')

    ax.set_xlabel('1 – Specificity (False Positive Rate)', fontsize=13)
    ax.set_ylabel('Sensitivity (True Positive Rate)', fontsize=13)
    ax.set_title(title, fontsize=15, fontweight='bold')
    ax.legend(loc='lower right', fontsize=11)
    ax.grid(True, alpha=0.3)
    ax.set_xlim([-0.02, 1.02])
    ax.set_ylim([-0.02, 1.05])

    plt.tight_layout()
    plt.savefig('roc_curves.png', dpi=300, bbox_inches='tight')
```

## 3. DeLong Test — Comparing Two AUCs

```python
# Compare 2 ROC curves statistically
# Python implementation
def delong_test(y_true, y_score1, y_score2):
    """
    DeLong et al. (1988) method for comparing AUCs
    Returns: z-statistic, p-value
    """
    from sklearn.metrics import roc_auc_score
    import numpy as np

    n = len(y_true)
    auc1 = roc_auc_score(y_true, y_score1)
    auc2 = roc_auc_score(y_true, y_score2)

    # Bootstrap-based comparison
    diffs = []
    for _ in range(5000):
        idx = np.random.choice(n, n, replace=True)
        try:
            a1 = roc_auc_score(y_true[idx], y_score1[idx])
            a2 = roc_auc_score(y_true[idx], y_score2[idx])
            diffs.append(a1 - a2)
        except:
            pass

    observed_diff = auc1 - auc2
    se = np.std(diffs)
    z = observed_diff / se
    p = 2 * (1 - stats.norm.cdf(abs(z)))

    print(f"AUC1 = {auc1:.3f}, AUC2 = {auc2:.3f}")
    print(f"Difference = {observed_diff:.3f} (95% CI: {observed_diff - 1.96*se:.3f}–{observed_diff + 1.96*se:.3f})")
    print(f"z = {z:.3f}, p = {p:.4f}")
    return z, p
```

## 4. Calibration Assessment

```python
from sklearn.calibration import calibration_curve

def plot_calibration(y_true, y_prob, model_name, n_bins=10):
    fraction_of_positives, mean_predicted_value = calibration_curve(
        y_true, y_prob, n_bins=n_bins, strategy='uniform'
    )

    fig, ax = plt.subplots(figsize=(8, 7))
    ax.plot(mean_predicted_value, fraction_of_positives,
            'b-o', linewidth=2, label=model_name)
    ax.plot([0, 1], [0, 1], 'k--', label='Perfect calibration')

    # Hosmer-Lemeshow test
    # Divide into 10 deciles
    df_cal = pd.DataFrame({'y_true': y_true, 'y_prob': y_prob})
    df_cal['decile'] = pd.qcut(df_cal['y_prob'], 10, labels=False, duplicates='drop')

    hl_stat = 0
    for decile in df_cal['decile'].unique():
        group = df_cal[df_cal['decile'] == decile]
        o = group['y_true'].sum()
        e = group['y_prob'].sum()
        hl_stat += (o - e)**2 / (e * (1 - e/len(group)))

    p_hl = 1 - stats.chi2.cdf(hl_stat, df=8)
    ax.text(0.05, 0.95, f'Hosmer-Lemeshow: χ²={hl_stat:.2f}, p={p_hl:.3f}',
            transform=ax.transAxes, fontsize=11,
            bbox=dict(boxstyle='round', facecolor='lightyellow'))

    ax.set_xlabel('Mean Predicted Probability', fontsize=13)
    ax.set_ylabel('Fraction of Positives', fontsize=13)
    ax.set_title('Calibration Plot', fontsize=15)
    ax.legend()
    plt.tight_layout()
    plt.savefig('calibration.png', dpi=300)
```

## 5. Decision Curve Analysis (DCA)

```python
def decision_curve_analysis(y_true, y_prob, thresholds=None):
    """
    DCA: ประเมิน clinical utility ของ prediction model
    """
    if thresholds is None:
        thresholds = np.linspace(0.01, 0.99, 99)

    n = len(y_true)
    prevalence = y_true.mean()
    net_benefits = []

    for threshold in thresholds:
        y_pred = (y_prob >= threshold).astype(int)
        tp = ((y_pred == 1) & (y_true == 1)).sum()
        fp = ((y_pred == 1) & (y_true == 0)).sum()
        nb = (tp/n) - (fp/n) * (threshold/(1-threshold))
        net_benefits.append(nb)

    # Treat all
    treat_all = [prevalence - (1-prevalence) * (t/(1-t)) for t in thresholds]

    fig, ax = plt.subplots(figsize=(10, 7))
    ax.plot(thresholds, net_benefits, 'b-', linewidth=2.5, label='Model')
    ax.plot(thresholds, treat_all, 'r--', linewidth=1.5, label='Treat all')
    ax.axhline(y=0, color='gray', linestyle='--', linewidth=1, label='Treat none')

    ax.set_xlabel('Threshold Probability', fontsize=13)
    ax.set_ylabel('Net Benefit', fontsize=13)
    ax.set_title('Decision Curve Analysis', fontsize=15)
    ax.legend(fontsize=12)
    ax.set_xlim([0, 1])
    ax.set_ylim([-0.05, max(net_benefits)*1.2])
    plt.tight_layout()
    plt.savefig('dca.png', dpi=300)
```

## 6. Results Reporting Template (STARD 2015)

```
The [index test] was evaluated against [reference standard] in [n] participants.
The area under the receiver operating characteristic curve (AUROC) was 0.XX
(95% CI 0.XX–0.XX). At the optimal cutpoint of [value] (Youden index = [value]),
the sensitivity was XX% (95% CI XX–XX%) and specificity was XX% (95% CI XX–XX%).
The positive predictive value was XX% (95% CI XX–XX%) and the negative predictive
value was XX% (95% CI XX–XX%). The positive and negative likelihood ratios were
X.X and X.XX, respectively, corresponding to a diagnostic odds ratio of X.X.
Calibration was assessed using the Hosmer-Lemeshow test (χ²([df]) = [value], p = [value]),
indicating [adequate/inadequate] calibration.
```
