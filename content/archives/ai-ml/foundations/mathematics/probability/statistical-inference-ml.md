---
title: "Statistical Inference for ML: Hypothesis Testing and Confidence Intervals"
description: "Master statistical inference for machine learning. Learn hypothesis testing, p-values, confidence intervals, and A/B testing for ML models with Python examples."
date: 2024-01-15
lastmod: 2024-01-15
draft: false
weight: 4
toc: true
categories: ["AI/ML", "Mathematics", "Statistics"]
tags: ["hypothesis testing", "p-values", "confidence intervals", "A/B testing", "statistical inference"]
series: ["Mathematics for ML"]
---

# Statistical Inference for ML: Hypothesis Testing and Confidence Intervals

**"Without statistical inference, we're just guessing. With it, we're making informed decisions with quantified uncertainty."**

Statistical inference lets us make decisions from data—is this model better? Is this feature significant? Is the A/B test result real or just noise? This guide covers everything you need for rigorous ML experimentation.

---

## Why Statistical Inference in ML?

### Common Questions Requiring Statistics

| Question | Statistical Tool |
|----------|-----------------|
| Is Model A better than Model B? | Hypothesis test (t-test) |
| How confident am I in model accuracy? | Confidence interval |
| Is this feature important? | Feature significance test |
| Did the new feature improve conversion? | A/B test |
| Is this accuracy difference real? | Statistical significance |

### The Problem: Randomness

```
Scenario:
- Model A accuracy: 85.2%
- Model B accuracy: 86.1%

Question: Is Model B really better, or is this just random noise?

Without statistics: "B is better! Ship it!"
With statistics: "Let's check if 0.9% difference is significant..."
```

---

## Hypothesis Testing Fundamentals

### The Framework

```
┌─────────────────────────────────────────────────────────────┐
│                    HYPOTHESIS TESTING                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  H₀ (Null Hypothesis):     No effect / No difference        │
│  H₁ (Alternative):         There IS an effect               │
│                                                             │
│  Procedure:                                                 │
│  1. Assume H₀ is true                                       │
│  2. Calculate test statistic from data                      │
│  3. Find p-value: P(data this extreme | H₀ true)           │
│  4. If p-value < α (usually 0.05), reject H₀               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Type I and Type II Errors

| | H₀ True (No Effect) | H₀ False (Effect Exists) |
|---|---|---|
| **Reject H₀** | Type I Error (False Positive) α | Correct! Power = 1-β |
| **Fail to Reject H₀** | Correct! | Type II Error (False Negative) β |

```python
import numpy as np
from scipy import stats
import matplotlib.pyplot as plt

def visualize_hypothesis_testing():
    """Visualize hypothesis testing concepts."""
    
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    
    # Distribution under H0 and H1
    ax = axes[0]
    x = np.linspace(-4, 8, 1000)
    
    # H0: μ = 0
    h0_dist = stats.norm.pdf(x, loc=0, scale=1)
    # H1: μ = 2 (true effect)
    h1_dist = stats.norm.pdf(x, loc=2, scale=1)
    
    ax.plot(x, h0_dist, 'b-', linewidth=2, label='H₀: μ = 0')
    ax.plot(x, h1_dist, 'r-', linewidth=2, label='H₁: μ = 2')
    
    # Critical value (α = 0.05, one-tailed)
    crit_val = stats.norm.ppf(0.95, loc=0, scale=1)
    
    # Type I error region (α)
    x_alpha = np.linspace(crit_val, 4, 100)
    ax.fill_between(x_alpha, stats.norm.pdf(x_alpha, 0, 1), 
                    alpha=0.3, color='blue', label=f'Type I Error (α)')
    
    # Type II error region (β)
    x_beta = np.linspace(-4, crit_val, 100)
    ax.fill_between(x_beta, stats.norm.pdf(x_beta, 2, 1),
                    alpha=0.3, color='red', label=f'Type II Error (β)')
    
    ax.axvline(crit_val, color='green', linestyle='--', 
               label=f'Critical value = {crit_val:.2f}')
    
    ax.set_xlabel('Test Statistic')
    ax.set_ylabel('Density')
    ax.set_title('Type I and Type II Errors')
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    # Power analysis
    ax = axes[1]
    effect_sizes = np.linspace(0, 3, 100)
    n_samples = [10, 30, 100, 300]
    
    for n in n_samples:
        # Power for two-sample t-test
        power = []
        for d in effect_sizes:
            # Non-centrality parameter
            ncp = d * np.sqrt(n / 2)
            # Power = P(reject H0 | H1 true)
            crit_t = stats.t.ppf(0.975, df=2*n-2)
            pwr = 1 - stats.nct.cdf(crit_t, df=2*n-2, nc=ncp)
            power.append(pwr)
        ax.plot(effect_sizes, power, label=f'n = {n}')
    
    ax.axhline(0.8, color='red', linestyle='--', alpha=0.5, label='Power = 0.8')
    ax.set_xlabel("Cohen's d (Effect Size)")
    ax.set_ylabel('Power')
    ax.set_title('Power Analysis: Sample Size Matters')
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    plt.tight_layout()
    plt.show()

visualize_hypothesis_testing()
```

---

## Common Statistical Tests for ML

### 1. T-Test: Comparing Means

```python
def compare_model_performance():
    """Compare two models using t-test."""
    
    np.random.seed(42)
    
    # Cross-validation scores
    n_folds = 10
    model_a_scores = np.random.normal(0.85, 0.03, n_folds)
    model_b_scores = np.random.normal(0.87, 0.03, n_folds)  # Slightly better
    
    # Paired t-test (same folds)
    t_stat, p_value = stats.ttest_rel(model_a_scores, model_b_scores)
    
    print("Model Comparison: Paired T-Test")
    print("=" * 50)
    print(f"Model A mean accuracy: {model_a_scores.mean():.4f} ± {model_a_scores.std():.4f}")
    print(f"Model B mean accuracy: {model_b_scores.mean():.4f} ± {model_b_scores.std():.4f}")
    print(f"Difference: {(model_b_scores.mean() - model_a_scores.mean()):.4f}")
    print(f"\nPaired t-test:")
    print(f"  t-statistic: {t_stat:.4f}")
    print(f"  p-value: {p_value:.4f}")
    
    alpha = 0.05
    if p_value < alpha:
        print(f"\n✓ Significant at α={alpha}: Model B is better!")
    else:
        print(f"\n✗ Not significant at α={alpha}: Cannot conclude B is better")
    
    # Effect size (Cohen's d for paired samples)
    diff = model_b_scores - model_a_scores
    cohens_d = diff.mean() / diff.std()
    print(f"\nCohen's d: {cohens_d:.2f}", end=" ")
    if abs(cohens_d) < 0.2:
        print("(negligible)")
    elif abs(cohens_d) < 0.5:
        print("(small)")
    elif abs(cohens_d) < 0.8:
        print("(medium)")
    else:
        print("(large)")
    
    # Visualize
    fig, axes = plt.subplots(1, 2, figsize=(12, 5))
    
    ax = axes[0]
    ax.boxplot([model_a_scores, model_b_scores], labels=['Model A', 'Model B'])
    ax.set_ylabel('Accuracy')
    ax.set_title('Model Performance Comparison')
    
    ax = axes[1]
    ax.scatter(model_a_scores, model_b_scores)
    ax.plot([0.75, 0.95], [0.75, 0.95], 'r--', label='Equal performance')
    ax.set_xlabel('Model A Accuracy')
    ax.set_ylabel('Model B Accuracy')
    ax.set_title('Paired Comparison (each point = one fold)')
    ax.legend()
    
    plt.tight_layout()
    plt.show()
    
    return t_stat, p_value

compare_model_performance()
```

### 2. McNemar's Test: Comparing Classifiers

For classification, McNemar's test compares errors:

```python
def mcnemar_test_example():
    """McNemar's test for comparing classifiers."""
    
    # Contingency table
    # Model B:     Correct    Wrong
    # Model A:
    # Correct        a          b
    # Wrong          c          d
    
    # Example: 1000 test samples
    a = 850  # Both correct
    b = 40   # A correct, B wrong
    c = 60   # A wrong, B correct
    d = 50   # Both wrong
    
    # McNemar statistic
    # Only b and c matter (disagreements)
    chi2 = (abs(b - c) - 1)**2 / (b + c)  # With continuity correction
    p_value = 1 - stats.chi2.cdf(chi2, df=1)
    
    print("McNemar's Test for Classifier Comparison")
    print("=" * 50)
    print(f"Contingency Table:")
    print(f"                    Model B")
    print(f"                 Correct  Wrong")
    print(f"Model A Correct    {a}      {b}")
    print(f"        Wrong      {c}      {d}")
    print(f"\nModel A accuracy: {(a+b)/(a+b+c+d)*100:.1f}%")
    print(f"Model B accuracy: {(a+c)/(a+b+c+d)*100:.1f}%")
    print(f"\nMcNemar χ²: {chi2:.4f}")
    print(f"p-value: {p_value:.4f}")
    
    if p_value < 0.05:
        if c > b:
            print("\n✓ Model B significantly better!")
        else:
            print("\n✓ Model A significantly better!")
    else:
        print("\n✗ No significant difference")
    
    # Using scipy
    from scipy.stats import chi2_contingency
    
    # Exact binomial test (for small samples)
    exact_p = stats.binom_test(b, b + c, 0.5)
    print(f"\nExact binomial test p-value: {exact_p:.4f}")

mcnemar_test_example()
```

### 3. Chi-Square Test: Categorical Data

```python
def chi_square_feature_importance():
    """Chi-square test for feature selection."""
    
    from sklearn.feature_selection import chi2
    from sklearn.datasets import load_iris
    from sklearn.preprocessing import MinMaxScaler
    
    # Load data
    iris = load_iris()
    X = MinMaxScaler().fit_transform(iris.data)  # Chi2 needs non-negative
    y = iris.target
    
    # Chi-square test
    chi2_scores, p_values = chi2(X, y)
    
    print("Chi-Square Test for Feature Importance")
    print("=" * 50)
    
    for i, (score, p, name) in enumerate(zip(chi2_scores, p_values, iris.feature_names)):
        sig = "***" if p < 0.001 else "**" if p < 0.01 else "*" if p < 0.05 else ""
        print(f"{name:20s}: χ² = {score:8.2f}, p = {p:.4f} {sig}")
    
    # Visualize
    fig, ax = plt.subplots(figsize=(10, 5))
    
    bars = ax.barh(iris.feature_names, chi2_scores, color='steelblue')
    ax.set_xlabel('Chi-Square Score')
    ax.set_title('Feature Importance (Chi-Square Test)')
    
    # Color bars by significance
    for bar, p in zip(bars, p_values):
        if p < 0.001:
            bar.set_color('darkgreen')
        elif p < 0.05:
            bar.set_color('green')
        else:
            bar.set_color('gray')
    
    plt.tight_layout()
    plt.show()

chi_square_feature_importance()
```

---

## Confidence Intervals

### Understanding Confidence Intervals

```python
def confidence_interval_demo():
    """Demonstrate confidence intervals."""
    
    np.random.seed(42)
    
    # True population parameter
    true_mean = 100
    true_std = 15
    
    # Sample
    n = 30
    sample = np.random.normal(true_mean, true_std, n)
    
    sample_mean = sample.mean()
    sample_std = sample.std(ddof=1)
    se = sample_std / np.sqrt(n)
    
    # 95% CI using t-distribution
    t_crit = stats.t.ppf(0.975, df=n-1)
    ci_lower = sample_mean - t_crit * se
    ci_upper = sample_mean + t_crit * se
    
    print("Confidence Interval Example")
    print("=" * 50)
    print(f"True population mean: {true_mean}")
    print(f"Sample size: {n}")
    print(f"Sample mean: {sample_mean:.2f}")
    print(f"Sample std: {sample_std:.2f}")
    print(f"Standard error: {se:.2f}")
    print(f"\n95% CI: [{ci_lower:.2f}, {ci_upper:.2f}]")
    print(f"Does CI contain true mean? {ci_lower < true_mean < ci_upper}")
    
    # Simulate many CIs
    n_simulations = 100
    fig, ax = plt.subplots(figsize=(12, 8))
    
    contains_true = 0
    for i in range(n_simulations):
        sample = np.random.normal(true_mean, true_std, n)
        m = sample.mean()
        se = sample.std(ddof=1) / np.sqrt(n)
        ci_l = m - t_crit * se
        ci_u = m + t_crit * se
        
        contains = ci_l < true_mean < ci_u
        if contains:
            contains_true += 1
            color = 'green'
        else:
            color = 'red'
        
        ax.plot([ci_l, ci_u], [i, i], color=color, alpha=0.5, linewidth=2)
        ax.plot(m, i, 'o', color=color, markersize=3)
    
    ax.axvline(true_mean, color='black', linestyle='--', linewidth=2, 
               label=f'True mean = {true_mean}')
    ax.set_xlabel('Value')
    ax.set_ylabel('Simulation')
    ax.set_title(f'95% CIs: {contains_true}% contain true mean')
    ax.legend()
    
    plt.tight_layout()
    plt.show()
    
    print(f"\nOut of {n_simulations} CIs, {contains_true}% contained true mean")
    print("(Expected: ~95%)")

confidence_interval_demo()
```

### CI for Model Accuracy

```python
def accuracy_confidence_interval():
    """Calculate CI for model accuracy."""
    
    # Model results
    n_correct = 850
    n_total = 1000
    accuracy = n_correct / n_total
    
    print("Confidence Interval for Accuracy")
    print("=" * 50)
    print(f"Accuracy: {n_correct}/{n_total} = {accuracy:.1%}")
    
    # Method 1: Normal approximation (Wald interval)
    se = np.sqrt(accuracy * (1 - accuracy) / n_total)
    z = 1.96  # 95% CI
    ci_wald = (accuracy - z * se, accuracy + z * se)
    
    # Method 2: Wilson score interval (better for extreme p)
    z2 = z**2
    center = (accuracy + z2/(2*n_total)) / (1 + z2/n_total)
    margin = z * np.sqrt((accuracy*(1-accuracy) + z2/(4*n_total))/n_total) / (1 + z2/n_total)
    ci_wilson = (center - margin, center + margin)
    
    # Method 3: Clopper-Pearson (exact, conservative)
    ci_exact = stats.binom.interval(0.95, n_total, accuracy)
    ci_exact = (ci_exact[0]/n_total, ci_exact[1]/n_total)
    
    print(f"\n95% Confidence Intervals:")
    print(f"  Wald (Normal):      [{ci_wald[0]:.4f}, {ci_wald[1]:.4f}]")
    print(f"  Wilson score:       [{ci_wilson[0]:.4f}, {ci_wilson[1]:.4f}]")
    print(f"  Clopper-Pearson:    [{ci_exact[0]:.4f}, {ci_exact[1]:.4f}]")
    
    print("\nRecommendation: Use Wilson for typical cases, Clopper-Pearson for publications")

accuracy_confidence_interval()
```

### Bootstrap Confidence Intervals

```python
def bootstrap_confidence_interval():
    """Bootstrap CI for any metric."""
    
    np.random.seed(42)
    
    # Sample data (e.g., individual prediction errors)
    true_errors = np.random.exponential(scale=0.1, size=200)
    
    # Metric: Median Absolute Error
    original_mae = np.median(true_errors)
    
    # Bootstrap
    n_bootstrap = 10000
    bootstrap_stats = []
    
    for _ in range(n_bootstrap):
        # Resample with replacement
        bootstrap_sample = np.random.choice(true_errors, size=len(true_errors), replace=True)
        bootstrap_stats.append(np.median(bootstrap_sample))
    
    bootstrap_stats = np.array(bootstrap_stats)
    
    # CI methods
    # Percentile method
    ci_percentile = (np.percentile(bootstrap_stats, 2.5), 
                     np.percentile(bootstrap_stats, 97.5))
    
    # BCa (Bias-Corrected and Accelerated) - simplified
    # For proper BCa, use scipy.stats.bootstrap
    
    print("Bootstrap Confidence Interval")
    print("=" * 50)
    print(f"Original Median Absolute Error: {original_mae:.4f}")
    print(f"Bootstrap mean: {bootstrap_stats.mean():.4f}")
    print(f"Bootstrap std: {bootstrap_stats.std():.4f}")
    print(f"\n95% CI (percentile): [{ci_percentile[0]:.4f}, {ci_percentile[1]:.4f}]")
    
    # Visualize
    fig, ax = plt.subplots(figsize=(10, 5))
    
    ax.hist(bootstrap_stats, bins=50, density=True, alpha=0.7, color='steelblue')
    ax.axvline(original_mae, color='red', linestyle='-', linewidth=2, 
               label=f'Original: {original_mae:.4f}')
    ax.axvline(ci_percentile[0], color='green', linestyle='--', linewidth=2)
    ax.axvline(ci_percentile[1], color='green', linestyle='--', linewidth=2,
               label=f'95% CI: [{ci_percentile[0]:.4f}, {ci_percentile[1]:.4f}]')
    
    ax.set_xlabel('Median Absolute Error')
    ax.set_ylabel('Density')
    ax.set_title('Bootstrap Distribution of MAE')
    ax.legend()
    
    plt.tight_layout()
    plt.show()

bootstrap_confidence_interval()
```

---

## A/B Testing for ML

### Complete A/B Test Framework

```python
def complete_ab_test():
    """Complete A/B testing framework."""
    
    np.random.seed(42)
    
    # Scenario: Testing new recommendation model
    # Metric: Click-through rate (CTR)
    
    # Control (Model A)
    n_control = 10000
    clicks_control = 520  # 5.2% CTR
    
    # Treatment (Model B)  
    n_treatment = 10000
    clicks_treatment = 580  # 5.8% CTR
    
    ctr_control = clicks_control / n_control
    ctr_treatment = clicks_treatment / n_treatment
    
    print("A/B Test: Recommendation Model")
    print("=" * 60)
    print(f"Control (A):   {clicks_control}/{n_control} = {ctr_control:.2%} CTR")
    print(f"Treatment (B): {clicks_treatment}/{n_treatment} = {ctr_treatment:.2%} CTR")
    print(f"Relative lift: {(ctr_treatment - ctr_control)/ctr_control:.1%}")
    
    # Method 1: Two-proportion z-test
    p_pooled = (clicks_control + clicks_treatment) / (n_control + n_treatment)
    se = np.sqrt(p_pooled * (1 - p_pooled) * (1/n_control + 1/n_treatment))
    z_stat = (ctr_treatment - ctr_control) / se
    p_value = 2 * (1 - stats.norm.cdf(abs(z_stat)))  # Two-tailed
    
    print(f"\nTwo-Proportion Z-Test:")
    print(f"  z-statistic: {z_stat:.3f}")
    print(f"  p-value: {p_value:.4f}")
    
    # Method 2: Chi-square test
    observed = np.array([[clicks_control, n_control - clicks_control],
                         [clicks_treatment, n_treatment - clicks_treatment]])
    chi2, p_chi2, dof, expected = stats.chi2_contingency(observed)
    
    print(f"\nChi-Square Test:")
    print(f"  χ²: {chi2:.3f}")
    print(f"  p-value: {p_chi2:.4f}")
    
    # Confidence interval for difference
    se_diff = np.sqrt(ctr_control*(1-ctr_control)/n_control + 
                      ctr_treatment*(1-ctr_treatment)/n_treatment)
    ci_diff = (ctr_treatment - ctr_control - 1.96*se_diff,
               ctr_treatment - ctr_control + 1.96*se_diff)
    
    print(f"\n95% CI for CTR difference: [{ci_diff[0]:.4f}, {ci_diff[1]:.4f}]")
    
    # Decision
    alpha = 0.05
    if p_value < alpha and ci_diff[0] > 0:
        print(f"\n✓ SIGNIFICANT: Model B is better (p < {alpha})")
        print("  Recommendation: Roll out Model B")
    else:
        print(f"\n✗ NOT SIGNIFICANT at α = {alpha}")
        print("  Recommendation: Continue testing or keep Model A")
    
    # Power analysis
    from scipy.stats import norm
    
    def sample_size_for_ab_test(baseline_rate, mde, alpha=0.05, power=0.8):
        """Calculate required sample size per group."""
        # mde = minimum detectable effect (relative)
        p1 = baseline_rate
        p2 = baseline_rate * (1 + mde)
        
        z_alpha = norm.ppf(1 - alpha/2)
        z_beta = norm.ppf(power)
        
        p_avg = (p1 + p2) / 2
        
        n = 2 * ((z_alpha * np.sqrt(2 * p_avg * (1 - p_avg)) + 
                  z_beta * np.sqrt(p1*(1-p1) + p2*(1-p2))) / (p2 - p1))**2
        
        return int(np.ceil(n))
    
    print("\n" + "=" * 60)
    print("Sample Size Calculator:")
    for mde in [0.05, 0.10, 0.20]:
        n_required = sample_size_for_ab_test(0.05, mde)
        print(f"  To detect {mde:.0%} lift with 80% power: {n_required:,} per group")

complete_ab_test()
```

### Sequential Testing

```python
def sequential_ab_test():
    """Sequential A/B testing (stop early)."""
    
    np.random.seed(42)
    
    # True effect (unknown in practice)
    true_ctr_a = 0.05
    true_ctr_b = 0.055  # 10% lift
    
    # Sequential test parameters
    max_samples = 20000
    check_interval = 500
    alpha = 0.05
    
    # Simulate data arrival
    n_a, n_b = 0, 0
    clicks_a, clicks_b = 0, 0
    
    p_values = []
    sample_sizes = []
    
    print("Sequential A/B Test")
    print("=" * 50)
    
    stopped_early = False
    
    for i in range(check_interval, max_samples + 1, check_interval):
        # New data
        new_clicks_a = np.random.binomial(check_interval // 2, true_ctr_a)
        new_clicks_b = np.random.binomial(check_interval // 2, true_ctr_b)
        
        clicks_a += new_clicks_a
        clicks_b += new_clicks_b
        n_a += check_interval // 2
        n_b += check_interval // 2
        
        # Calculate p-value
        p_pooled = (clicks_a + clicks_b) / (n_a + n_b)
        se = np.sqrt(p_pooled * (1-p_pooled) * (1/n_a + 1/n_b))
        if se > 0:
            z = (clicks_b/n_b - clicks_a/n_a) / se
            p_val = 2 * (1 - stats.norm.cdf(abs(z)))
        else:
            p_val = 1.0
        
        p_values.append(p_val)
        sample_sizes.append(i)
        
        # Alpha spending (O'Brien-Fleming approximation)
        # More stringent early, relaxed late
        spent_alpha = alpha * np.sqrt(check_interval / i)
        
        if p_val < spent_alpha:
            print(f"Sample {i}: p = {p_val:.4f} < {spent_alpha:.4f} → STOP!")
            stopped_early = True
            break
    
    if not stopped_early:
        print(f"Reached max samples ({max_samples})")
    
    print(f"\nFinal: A={clicks_a}/{n_a}={clicks_a/n_a:.4f}, B={clicks_b}/{n_b}={clicks_b/n_b:.4f}")
    print(f"Saved {max_samples - sample_sizes[-1]} samples by stopping early!")
    
    # Visualize
    fig, ax = plt.subplots(figsize=(12, 5))
    
    ax.plot(sample_sizes, p_values, 'b-', linewidth=2, label='p-value')
    ax.axhline(0.05, color='red', linestyle='--', label='α = 0.05')
    
    # Alpha spending boundary
    boundaries = [alpha * np.sqrt(check_interval / n) for n in sample_sizes]
    ax.plot(sample_sizes, boundaries, 'g--', label='Alpha spending boundary')
    
    ax.set_xlabel('Total Sample Size')
    ax.set_ylabel('p-value')
    ax.set_title('Sequential A/B Testing')
    ax.legend()
    ax.set_yscale('log')
    ax.grid(True, alpha=0.3)
    
    plt.tight_layout()
    plt.show()

sequential_ab_test()
```

---

## Multiple Testing Correction

### The Multiple Comparisons Problem

```python
def multiple_testing_problem():
    """Demonstrate and correct multiple testing problem."""
    
    np.random.seed(42)
    
    # Scenario: Testing 20 features for significance
    n_tests = 20
    n_samples = 100
    
    # Generate data (no real effect!)
    p_values = []
    for _ in range(n_tests):
        group_a = np.random.normal(0, 1, n_samples)
        group_b = np.random.normal(0, 1, n_samples)  # Same distribution!
        _, p = stats.ttest_ind(group_a, group_b)
        p_values.append(p)
    
    p_values = np.array(p_values)
    
    print("Multiple Testing Problem")
    print("=" * 50)
    print(f"Testing {n_tests} features (no real effects)")
    print(f"Uncorrected: {(p_values < 0.05).sum()} 'significant' (false positives!)")
    
    # Bonferroni correction
    alpha_bonf = 0.05 / n_tests
    sig_bonf = (p_values < alpha_bonf).sum()
    
    # Benjamini-Hochberg (FDR control)
    def benjamini_hochberg(p_values, alpha=0.05):
        n = len(p_values)
        sorted_idx = np.argsort(p_values)
        sorted_p = p_values[sorted_idx]
        
        # BH threshold
        thresholds = alpha * np.arange(1, n+1) / n
        
        # Find largest p below threshold
        significant = sorted_p <= thresholds
        if significant.any():
            max_idx = np.max(np.where(significant)[0])
            return sorted_idx[:max_idx+1]
        return np.array([])
    
    sig_bh = benjamini_hochberg(p_values)
    
    # Holm-Bonferroni (step-down)
    def holm_bonferroni(p_values, alpha=0.05):
        n = len(p_values)
        sorted_idx = np.argsort(p_values)
        sorted_p = p_values[sorted_idx]
        
        significant = []
        for i, (idx, p) in enumerate(zip(sorted_idx, sorted_p)):
            threshold = alpha / (n - i)
            if p < threshold:
                significant.append(idx)
            else:
                break
        return np.array(significant)
    
    sig_holm = holm_bonferroni(p_values)
    
    print(f"\nCorrection methods:")
    print(f"  Bonferroni (α/{n_tests}={alpha_bonf:.4f}): {sig_bonf} significant")
    print(f"  Holm-Bonferroni: {len(sig_holm)} significant")
    print(f"  Benjamini-Hochberg (FDR): {len(sig_bh)} significant")
    
    # Visualize
    fig, ax = plt.subplots(figsize=(12, 5))
    
    sorted_p = np.sort(p_values)
    x = np.arange(1, n_tests + 1)
    
    ax.scatter(x, sorted_p, s=100, label='Sorted p-values')
    ax.axhline(0.05, color='red', linestyle='--', label='α = 0.05')
    ax.axhline(alpha_bonf, color='orange', linestyle='--', 
               label=f'Bonferroni: {alpha_bonf:.4f}')
    
    # BH line
    bh_threshold = 0.05 * x / n_tests
    ax.plot(x, bh_threshold, 'g--', label='BH threshold')
    
    ax.set_xlabel('Rank')
    ax.set_ylabel('p-value')
    ax.set_title('Multiple Testing Correction')
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    plt.tight_layout()
    plt.show()

multiple_testing_problem()
```

---

## Practical Guidelines

### Sample Size Planning

```python
def sample_size_calculator():
    """Sample size calculator for common scenarios."""
    
    from scipy.stats import norm
    
    print("Sample Size Calculator")
    print("=" * 60)
    
    # Parameters
    alpha = 0.05
    power = 0.80
    
    z_alpha = norm.ppf(1 - alpha/2)
    z_beta = norm.ppf(power)
    
    # 1. Two-sample t-test
    def n_ttest(effect_size, alpha=0.05, power=0.8):
        z_a = norm.ppf(1 - alpha/2)
        z_b = norm.ppf(power)
        return 2 * ((z_a + z_b) / effect_size) ** 2
    
    print("\n1. Two-Sample T-Test (detect difference in means)")
    for d in [0.2, 0.5, 0.8]:  # Small, medium, large effect
        n = n_ttest(d)
        print(f"   Cohen's d = {d}: n = {int(np.ceil(n))} per group")
    
    # 2. Proportion test (A/B test)
    def n_proportion(p1, mde):
        p2 = p1 * (1 + mde)
        p_avg = (p1 + p2) / 2
        n = 2 * ((z_alpha * np.sqrt(2*p_avg*(1-p_avg)) + 
                  z_beta * np.sqrt(p1*(1-p1) + p2*(1-p2))) / (p2-p1))**2
        return n
    
    print("\n2. A/B Test (baseline CTR = 5%)")
    for mde in [0.05, 0.10, 0.20]:
        n = n_proportion(0.05, mde)
        print(f"   Detect {mde:.0%} lift: n = {int(np.ceil(n)):,} per group")
    
    # 3. Model comparison (paired)
    def n_paired(effect_size, corr=0.5):
        # For paired test, effective sample size is larger
        return n_ttest(effect_size) * (1 - corr)
    
    print("\n3. Paired Model Comparison (10-fold CV)")
    for d in [0.2, 0.5, 0.8]:
        n = n_paired(d)
        print(f"   Effect d = {d}: n = {int(np.ceil(n))} folds")

sample_size_calculator()
```

### Reporting Guidelines

```python
def reporting_checklist():
    """Statistical reporting checklist for ML papers."""
    
    print("Statistical Reporting Checklist for ML Papers")
    print("=" * 60)
    
    checklist = [
        ("Sample sizes", "Report n for all experiments"),
        ("Effect sizes", "Report Cohen's d or similar, not just p-values"),
        ("Confidence intervals", "Prefer over just p-values"),
        ("Multiple testing", "Report correction method if applicable"),
        ("Random seeds", "Report seeds for reproducibility"),
        ("Cross-validation", "Report mean ± std across folds"),
        ("Significance tests", "Report test name, statistic, p-value"),
        ("Assumptions", "State and verify test assumptions"),
        ("Power analysis", "Justify sample sizes a priori"),
    ]
    
    for item, description in checklist:
        print(f"☐ {item:20s}: {description}")
    
    print("\n" + "=" * 60)
    print("Example reporting:")
    print("""
    'Model B achieved higher accuracy than Model A 
    (86.2% vs 85.1%, difference = 1.1%, 95% CI [0.3%, 1.9%]). 
    A paired t-test across 10 CV folds showed the difference 
    was statistically significant (t(9) = 3.21, p = 0.011, 
    Cohen's d = 0.72, medium effect). Results were consistent 
    across three random seeds.'
    """)

reporting_checklist()
```

---

## FAQs

### What does "statistically significant" mean?

It means the observed result is unlikely to occur by chance alone (typically <5% probability). It does NOT mean:
- The effect is large or important
- The result is practically meaningful
- The experiment is well-designed

### Why is p = 0.05 the standard threshold?

It's a convention, not a law. R.A. Fisher suggested it as "convenient." Many fields now use stricter thresholds (p < 0.001 for physics discoveries).

### What's the difference between statistical and practical significance?

- **Statistical**: "Is this difference real?"
- **Practical**: "Is this difference meaningful?"

A 0.1% accuracy improvement might be statistically significant with enough data, but not worth the engineering effort.

---

## Key Takeaways

1. **Hypothesis testing** quantifies whether observed differences are real
2. **p-value** = probability of data if null hypothesis is true
3. **Confidence intervals** > p-values for interpretation
4. **Effect size** tells you if it matters practically
5. **Multiple testing** requires correction (Bonferroni, BH)
6. **Sample size** planning prevents underpowered experiments

---

## Next Steps

Continue your statistics journey:

1. **[Probability Distributions](/docs/ai-ml/foundations/mathematics/probability/probability-distributions-machine-learning/)** - Foundation concepts
2. **[Bayes' Theorem](/docs/ai-ml/foundations/mathematics/probability/bayes-theorem-explained/)** - Bayesian approach
3. **[Information Theory](/docs/ai-ml/foundations/mathematics/probability/information-theory-entropy/)** - Entropy and divergence

---

## References

1. Wasserman, L. "All of Statistics" - Chapters 9-11
2. Cohen, J. "Statistical Power Analysis for the Behavioral Sciences"
3. Kohavi, R. et al. "Trustworthy Online Controlled Experiments" (2020)
4. Dror, R. et al. "Deep Dominance - How to Properly Compare Deep NLP Models" (2019)

---

*Last updated: January 2024. Part of our [Mathematics for Machine Learning](/docs/ai-ml/foundations/mathematics/) series.*
