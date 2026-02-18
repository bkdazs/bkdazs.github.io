---
title: "Probability Distributions for Machine Learning: Complete Guide"
description: "Master probability distributions essential for ML. Learn Gaussian, Bernoulli, Poisson, and more with Python implementations, real-world examples, and visual explanations."
date: 2024-01-15
lastmod: 2024-01-15
draft: false
weight: 1
toc: true
categories: ["AI/ML", "Mathematics", "Statistics"]
tags: ["probability distributions", "gaussian distribution", "bernoulli", "machine learning", "statistics"]
series: ["Mathematics for ML"]
---

# Probability Distributions for Machine Learning: Complete Guide

**"In machine learning, everything is a distribution waiting to be discovered."**

Probability distributions are the foundation of machine learning. From modeling uncertainty in predictions to defining loss functions and understanding data—distributions appear everywhere. This comprehensive guide covers every distribution you'll need for ML.

---

## Why Probability Distributions Matter in ML

### Every ML Problem Has Distributions

| ML Task | Distribution Used |
|---------|------------------|
| Binary classification | Bernoulli |
| Multi-class classification | Categorical/Multinomial |
| Regression | Gaussian |
| Count prediction | Poisson |
| Survival analysis | Exponential/Weibull |
| Topic modeling | Dirichlet |
| Sequence modeling | Markov chains |
| Generative models | VAE: Gaussian, GAN: Implicit |

### Where Distributions Appear

```
┌─────────────────────────────────────────────────────────┐
│                  ML PIPELINE                             │
├─────────────────────────────────────────────────────────┤
│  DATA          →  Often assumed Gaussian, check!         │
│  FEATURES      →  Normalize to standard Gaussian         │
│  MODEL         →  Output distribution (softmax = Cat.)   │
│  LOSS          →  Derived from distribution (NLL)        │
│  INFERENCE     →  Posterior distribution (Bayesian)      │
│  UNCERTAINTY   →  Prediction distribution (epistemic)    │
└─────────────────────────────────────────────────────────┘
```

---

## Fundamentals: PDF, PMF, and CDF

### Probability Mass Function (PMF) - Discrete

For discrete random variables (coin flip, dice):

$$P(X = x) = \text{probability of exactly } x$$

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy import stats

def plot_pmf_example():
    """Visualize PMF for a dice roll."""
    x = np.array([1, 2, 3, 4, 5, 6])
    pmf = np.array([1/6] * 6)
    
    fig, ax = plt.subplots(figsize=(10, 5))
    ax.bar(x, pmf, color='steelblue', alpha=0.7)
    ax.set_xlabel('Outcome')
    ax.set_ylabel('P(X = x)')
    ax.set_title('PMF: Fair Dice Roll')
    ax.set_xticks(x)
    ax.set_ylim(0, 0.25)
    for i, p in enumerate(pmf):
        ax.text(x[i], p + 0.01, f'{p:.3f}', ha='center')
    plt.show()

plot_pmf_example()
```

### Probability Density Function (PDF) - Continuous

For continuous random variables (height, temperature):

$$f(x) = \frac{dF(x)}{dx}$$

**Key insight**: $P(X = x) = 0$ for continuous variables! Instead:
$$P(a \leq X \leq b) = \int_a^b f(x) dx$$

```python
def plot_pdf_example():
    """Visualize PDF for Gaussian distribution."""
    x = np.linspace(-4, 4, 1000)
    pdf = stats.norm.pdf(x, loc=0, scale=1)
    
    fig, ax = plt.subplots(figsize=(10, 5))
    ax.plot(x, pdf, 'b-', linewidth=2)
    ax.fill_between(x, pdf, alpha=0.3)
    
    # Highlight probability for a region
    x_region = np.linspace(-1, 1, 100)
    ax.fill_between(x_region, stats.norm.pdf(x_region), 
                    alpha=0.6, color='orange', 
                    label=f'P(-1 ≤ X ≤ 1) = {stats.norm.cdf(1) - stats.norm.cdf(-1):.3f}')
    
    ax.set_xlabel('x')
    ax.set_ylabel('f(x)')
    ax.set_title('PDF: Standard Normal Distribution')
    ax.legend()
    plt.show()

plot_pdf_example()
```

### Cumulative Distribution Function (CDF)

$$F(x) = P(X \leq x)$$

CDF is always:
- Non-decreasing
- $\lim_{x \to -\infty} F(x) = 0$
- $\lim_{x \to +\infty} F(x) = 1$

```python
def plot_cdf_comparison():
    """Compare CDF for different distributions."""
    x = np.linspace(-4, 4, 1000)
    
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    
    # Normal CDF
    ax = axes[0]
    for sigma in [0.5, 1.0, 2.0]:
        cdf = stats.norm.cdf(x, loc=0, scale=sigma)
        ax.plot(x, cdf, label=f'σ = {sigma}')
    ax.axhline(0.5, color='gray', linestyle='--', alpha=0.5)
    ax.set_xlabel('x')
    ax.set_ylabel('F(x)')
    ax.set_title('CDF: Normal Distribution')
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    # Logistic CDF (sigmoid!)
    ax = axes[1]
    cdf = stats.logistic.cdf(x, loc=0, scale=1)
    ax.plot(x, cdf, 'r-', linewidth=2, label='Logistic (Sigmoid!)')
    ax.set_xlabel('x')
    ax.set_ylabel('F(x)')
    ax.set_title('CDF: Logistic Distribution = Sigmoid Function')
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    plt.tight_layout()
    plt.show()

plot_cdf_comparison()
```

---

## Discrete Distributions

### 1. Bernoulli Distribution

**When to use**: Binary outcomes (success/failure, spam/not spam)

$$P(X = 1) = p, \quad P(X = 0) = 1 - p$$

**Parameters**: $p \in [0, 1]$

**Properties**:
- $E[X] = p$
- $\text{Var}(X) = p(1-p)$

```python
def bernoulli_examples():
    """Bernoulli distribution in ML."""
    
    # Example: Email spam classification
    # Model outputs p = P(spam)
    p_spam = 0.85
    
    # Bernoulli PMF
    print("Bernoulli Distribution (Spam Detection)")
    print(f"P(spam=1) = {p_spam}")
    print(f"P(spam=0) = {1-p_spam}")
    print(f"Expected value = {p_spam}")
    print(f"Variance = {p_spam * (1-p_spam):.4f}")
    
    # Binary Cross-Entropy = Negative Log-Likelihood of Bernoulli
    y_true = 1  # Actually spam
    bce = -(y_true * np.log(p_spam) + (1-y_true) * np.log(1-p_spam))
    print(f"\nBinary Cross-Entropy Loss = {bce:.4f}")
    
    # Connection to sigmoid
    logit = 1.5  # Model output before sigmoid
    p = 1 / (1 + np.exp(-logit))  # Sigmoid
    print(f"\nSigmoid({logit}) = {p:.4f}")

bernoulli_examples()
```

### 2. Binomial Distribution

**When to use**: Count of successes in n independent trials

$$P(X = k) = \binom{n}{k} p^k (1-p)^{n-k}$$

**Parameters**: $n$ (trials), $p$ (success probability)

**Properties**:
- $E[X] = np$
- $\text{Var}(X) = np(1-p)$

```python
def binomial_examples():
    """Binomial distribution in ML."""
    
    # Example: Out of 100 users, how many will click an ad?
    n_users = 100
    p_click = 0.05
    
    k = np.arange(0, 20)
    pmf = stats.binom.pmf(k, n_users, p_click)
    
    fig, ax = plt.subplots(figsize=(12, 5))
    ax.bar(k, pmf, color='steelblue', alpha=0.7)
    ax.set_xlabel('Number of Clicks')
    ax.set_ylabel('Probability')
    ax.set_title(f'Binomial Distribution: {n_users} users, {p_click*100}% click rate')
    
    # Mark expected value
    expected = n_users * p_click
    ax.axvline(expected, color='red', linestyle='--', 
               label=f'E[X] = {expected}')
    ax.legend()
    plt.show()
    
    # Calculate probabilities
    print(f"P(X ≥ 10) = {1 - stats.binom.cdf(9, n_users, p_click):.4f}")
    print(f"P(X = 5) = {stats.binom.pmf(5, n_users, p_click):.4f}")

binomial_examples()
```

### 3. Categorical/Multinomial Distribution

**When to use**: Multi-class classification (MNIST, ImageNet)

$$P(X = k) = p_k, \quad \sum_{k=1}^{K} p_k = 1$$

**This is what softmax represents!**

```python
def categorical_softmax():
    """Categorical distribution = Softmax output."""
    
    # Neural network output (logits)
    logits = np.array([2.0, 1.0, 0.1])  # 3 classes
    
    # Softmax = normalized probabilities
    def softmax(x):
        exp_x = np.exp(x - np.max(x))  # Stability trick
        return exp_x / exp_x.sum()
    
    probs = softmax(logits)
    
    print("Categorical Distribution (Multi-class Classification)")
    print("=" * 50)
    print(f"Logits: {logits}")
    print(f"Softmax (Categorical probs): {probs.round(4)}")
    print(f"Sum: {probs.sum():.4f}")
    
    # Cross-entropy loss = Negative log-likelihood of Categorical
    y_true = 0  # True class is 0
    ce_loss = -np.log(probs[y_true])
    print(f"\nCross-Entropy Loss = {ce_loss:.4f}")
    
    # Sample from categorical
    samples = np.random.choice(3, size=1000, p=probs)
    print(f"\nSampling 1000 times:")
    for i in range(3):
        print(f"  Class {i}: {(samples == i).sum()} samples ({probs[i]*100:.1f}% expected)")

categorical_softmax()
```

### 4. Poisson Distribution

**When to use**: Count of events in a fixed interval (website visits, rare events)

$$P(X = k) = \frac{\lambda^k e^{-\lambda}}{k!}$$

**Parameters**: $\lambda > 0$ (rate)

**Properties**:
- $E[X] = \lambda$
- $\text{Var}(X) = \lambda$

```python
def poisson_examples():
    """Poisson distribution in ML."""
    
    # Example: Number of website visits per hour
    lambda_visits = 5
    
    k = np.arange(0, 20)
    pmf = stats.poisson.pmf(k, lambda_visits)
    
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    
    # PMF
    ax = axes[0]
    ax.bar(k, pmf, color='green', alpha=0.7)
    ax.axvline(lambda_visits, color='red', linestyle='--', 
               label=f'λ = E[X] = Var(X) = {lambda_visits}')
    ax.set_xlabel('Number of Visits')
    ax.set_ylabel('Probability')
    ax.set_title('Poisson PMF: Website Visits per Hour')
    ax.legend()
    
    # Different lambdas
    ax = axes[1]
    for lam in [1, 5, 10, 20]:
        pmf = stats.poisson.pmf(k, lam)
        ax.plot(k, pmf, 'o-', label=f'λ = {lam}')
    ax.set_xlabel('k')
    ax.set_ylabel('P(X = k)')
    ax.set_title('Poisson Distribution: Effect of λ')
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    plt.tight_layout()
    plt.show()

poisson_examples()
```

### 5. Geometric Distribution

**When to use**: Number of trials until first success

$$P(X = k) = (1-p)^{k-1} p$$

```python
def geometric_example():
    """Geometric: trials until first success."""
    
    p_success = 0.2  # 20% success rate
    
    k = np.arange(1, 25)
    pmf = stats.geom.pmf(k, p_success)
    
    plt.figure(figsize=(10, 5))
    plt.bar(k, pmf, color='purple', alpha=0.7)
    plt.xlabel('Number of Trials Until First Success')
    plt.ylabel('Probability')
    plt.title(f'Geometric Distribution (p = {p_success})')
    plt.axvline(1/p_success, color='red', linestyle='--', 
                label=f'E[X] = 1/p = {1/p_success}')
    plt.legend()
    plt.show()
    
    print(f"Expected trials until success: {1/p_success}")
    print(f"P(success on first try): {stats.geom.pmf(1, p_success):.4f}")

geometric_example()
```

---

## Continuous Distributions

### 1. Gaussian (Normal) Distribution

**The most important distribution in ML!**

$$f(x) = \frac{1}{\sqrt{2\pi\sigma^2}} \exp\left(-\frac{(x-\mu)^2}{2\sigma^2}\right)$$

**Parameters**: $\mu$ (mean), $\sigma^2$ (variance)

**Why so important?**
- Central Limit Theorem
- Maximum entropy for given mean/variance
- Gaussian noise assumption
- Analytical tractability

```python
def gaussian_comprehensive():
    """Everything about Gaussian distribution."""
    
    fig, axes = plt.subplots(2, 2, figsize=(14, 10))
    x = np.linspace(-5, 5, 1000)
    
    # 1. Different means
    ax = axes[0, 0]
    for mu in [-2, 0, 2]:
        pdf = stats.norm.pdf(x, loc=mu, scale=1)
        ax.plot(x, pdf, label=f'μ = {mu}')
    ax.set_title('Effect of Mean μ')
    ax.set_xlabel('x')
    ax.set_ylabel('f(x)')
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    # 2. Different variances
    ax = axes[0, 1]
    for sigma in [0.5, 1, 2]:
        pdf = stats.norm.pdf(x, loc=0, scale=sigma)
        ax.plot(x, pdf, label=f'σ = {sigma}')
    ax.set_title('Effect of Standard Deviation σ')
    ax.set_xlabel('x')
    ax.set_ylabel('f(x)')
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    # 3. 68-95-99.7 rule
    ax = axes[1, 0]
    pdf = stats.norm.pdf(x, loc=0, scale=1)
    ax.plot(x, pdf, 'b-', linewidth=2)
    
    for n_sigma, color, alpha in [(1, 'red', 0.3), (2, 'orange', 0.2), (3, 'yellow', 0.1)]:
        x_fill = np.linspace(-n_sigma, n_sigma, 100)
        ax.fill_between(x_fill, stats.norm.pdf(x_fill), alpha=alpha, color=color,
                       label=f'±{n_sigma}σ: {(stats.norm.cdf(n_sigma) - stats.norm.cdf(-n_sigma))*100:.1f}%')
    
    ax.set_title('68-95-99.7 Rule')
    ax.set_xlabel('x')
    ax.set_ylabel('f(x)')
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    # 4. Q-Q plot concept
    ax = axes[1, 1]
    np.random.seed(42)
    normal_samples = np.random.normal(0, 1, 1000)
    stats.probplot(normal_samples, dist="norm", plot=ax)
    ax.set_title('Q-Q Plot: Check if Data is Gaussian')
    
    plt.tight_layout()
    plt.show()

gaussian_comprehensive()
```

### MSE Loss = Gaussian MLE

```python
def mse_is_gaussian_mle():
    """Show that MSE loss comes from Gaussian assumption."""
    
    print("MSE Loss = Maximum Likelihood under Gaussian Noise")
    print("=" * 60)
    
    # Assume: y = f(x) + ε, where ε ~ N(0, σ²)
    # Then: y | x ~ N(f(x), σ²)
    
    # Log-likelihood:
    # log p(y|x) = -1/(2σ²) * (y - f(x))² - log(σ√(2π))
    
    # Maximizing log-likelihood = Minimizing (y - f(x))² = MSE!
    
    np.random.seed(42)
    x = np.linspace(0, 10, 100)
    y_true = 2 * x + 3
    y_noisy = y_true + np.random.normal(0, 2, size=100)  # Add Gaussian noise
    
    # Fit linear regression (MLE)
    from numpy.polynomial.polynomial import polyfit
    b, m = polyfit(x, y_noisy, 1)
    y_pred = b + m * x
    
    # MSE
    mse = np.mean((y_noisy - y_pred) ** 2)
    
    print(f"True: y = 2x + 3")
    print(f"Fitted: y = {m:.2f}x + {b:.2f}")
    print(f"MSE: {mse:.4f}")
    
    # Visual
    plt.figure(figsize=(10, 5))
    plt.scatter(x, y_noisy, alpha=0.5, label='Noisy data')
    plt.plot(x, y_true, 'g-', linewidth=2, label='True line')
    plt.plot(x, y_pred, 'r--', linewidth=2, label='MLE fit (MSE)')
    plt.xlabel('x')
    plt.ylabel('y')
    plt.title('Regression = MLE under Gaussian Noise')
    plt.legend()
    plt.show()

mse_is_gaussian_mle()
```

### 2. Uniform Distribution

$$f(x) = \frac{1}{b-a} \text{ for } x \in [a, b]$$

```python
def uniform_in_ml():
    """Uniform distribution uses in ML."""
    
    print("Uniform Distribution in ML")
    print("=" * 50)
    
    # 1. Weight initialization (Glorot/Xavier uniform)
    fan_in, fan_out = 784, 256
    limit = np.sqrt(6 / (fan_in + fan_out))
    weights = np.random.uniform(-limit, limit, size=(fan_in, fan_out))
    print(f"Xavier Uniform Init: U(-{limit:.4f}, {limit:.4f})")
    
    # 2. Dropout random mask
    keep_prob = 0.8
    mask = np.random.uniform(0, 1, size=100) < keep_prob
    print(f"Dropout mask (keep_prob={keep_prob}): {mask.sum()} kept out of 100")
    
    # 3. Random search hyperparameters
    lr = np.random.uniform(1e-4, 1e-2)
    print(f"Random search learning rate: {lr:.6f}")

uniform_in_ml()
```

### 3. Exponential Distribution

**When to use**: Time between events, survival analysis

$$f(x) = \lambda e^{-\lambda x} \text{ for } x \geq 0$$

**Properties**:
- $E[X] = 1/\lambda$
- $\text{Var}(X) = 1/\lambda^2$
- Memoryless property!

```python
def exponential_examples():
    """Exponential distribution examples."""
    
    # Example: Time between customer arrivals
    lambda_rate = 0.5  # 0.5 arrivals per minute (1 every 2 min)
    
    x = np.linspace(0, 10, 1000)
    pdf = stats.expon.pdf(x, scale=1/lambda_rate)
    
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    
    ax = axes[0]
    ax.plot(x, pdf, 'b-', linewidth=2)
    ax.fill_between(x, pdf, alpha=0.3)
    ax.axvline(1/lambda_rate, color='red', linestyle='--',
               label=f'E[X] = 1/λ = {1/lambda_rate} min')
    ax.set_xlabel('Time (minutes)')
    ax.set_ylabel('f(x)')
    ax.set_title(f'Exponential Distribution (λ = {lambda_rate})')
    ax.legend()
    
    # Survival function
    ax = axes[1]
    survival = 1 - stats.expon.cdf(x, scale=1/lambda_rate)
    ax.plot(x, survival, 'r-', linewidth=2)
    ax.set_xlabel('Time (minutes)')
    ax.set_ylabel('P(X > x)')
    ax.set_title('Survival Function: P(wait > x minutes)')
    ax.grid(True, alpha=0.3)
    
    plt.tight_layout()
    plt.show()
    
    print(f"P(wait > 3 min) = {1 - stats.expon.cdf(3, scale=1/lambda_rate):.4f}")

exponential_examples()
```

### 4. Beta Distribution

**When to use**: Modeling probabilities, Bayesian priors

$$f(x; \alpha, \beta) = \frac{x^{\alpha-1}(1-x)^{\beta-1}}{B(\alpha, \beta)}$$

**Parameters**: $\alpha, \beta > 0$

**Key**: $x \in [0, 1]$ — perfect for probabilities!

```python
def beta_distribution_examples():
    """Beta distribution: modeling probabilities."""
    
    x = np.linspace(0.001, 0.999, 1000)
    
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    
    # Different shapes
    ax = axes[0]
    params = [(0.5, 0.5), (1, 1), (2, 2), (5, 2), (2, 5), (10, 10)]
    for alpha, beta in params:
        pdf = stats.beta.pdf(x, alpha, beta)
        ax.plot(x, pdf, label=f'α={alpha}, β={beta}')
    ax.set_xlabel('x')
    ax.set_ylabel('f(x)')
    ax.set_title('Beta Distribution: Different Parameters')
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    # Bayesian updating
    ax = axes[1]
    
    # Prior: Beta(2, 2) - slight belief towards 0.5
    alpha_prior, beta_prior = 2, 2
    
    # Data: 8 successes out of 10 trials
    successes, failures = 8, 2
    
    # Posterior: Beta(alpha + successes, beta + failures)
    alpha_post = alpha_prior + successes
    beta_post = beta_prior + failures
    
    prior = stats.beta.pdf(x, alpha_prior, beta_prior)
    posterior = stats.beta.pdf(x, alpha_post, beta_post)
    
    ax.plot(x, prior, 'b-', label=f'Prior: Beta({alpha_prior}, {beta_prior})')
    ax.plot(x, posterior, 'r-', label=f'Posterior: Beta({alpha_post}, {beta_post})')
    ax.axvline(successes/10, color='green', linestyle='--', label='MLE: 8/10 = 0.8')
    ax.set_xlabel('Probability of Success')
    ax.set_ylabel('Density')
    ax.set_title('Bayesian Updating with Beta Distribution')
    ax.legend()
    
    plt.tight_layout()
    plt.show()
    
    print(f"Prior mean: {alpha_prior/(alpha_prior+beta_prior):.4f}")
    print(f"Posterior mean: {alpha_post/(alpha_post+beta_post):.4f}")
    print(f"MLE: {successes/(successes+failures):.4f}")

beta_distribution_examples()
```

### 5. Gamma Distribution

**When to use**: Positive continuous values, waiting times, Bayesian priors

$$f(x; k, \theta) = \frac{x^{k-1}e^{-x/\theta}}{\theta^k \Gamma(k)}$$

**Relationships**:
- $k=1$: Exponential
- Integer $k$: Sum of $k$ exponentials (Erlang)
- Chi-squared: $\chi^2_n = \text{Gamma}(n/2, 2)$

```python
def gamma_distribution():
    """Gamma distribution examples."""
    
    x = np.linspace(0.001, 20, 1000)
    
    fig, ax = plt.subplots(figsize=(10, 6))
    
    params = [(1, 2), (2, 2), (5, 1), (9, 0.5)]
    for k, theta in params:
        pdf = stats.gamma.pdf(x, a=k, scale=theta)
        ax.plot(x, pdf, label=f'k={k}, θ={theta}')
    
    ax.set_xlabel('x')
    ax.set_ylabel('f(x)')
    ax.set_title('Gamma Distribution')
    ax.legend()
    ax.grid(True, alpha=0.3)
    plt.show()

gamma_distribution()
```

### 6. Student's t-Distribution

**When to use**: Robust regression, small samples, heavy tails

$$f(x; \nu) = \frac{\Gamma((\nu+1)/2)}{\sqrt{\nu\pi}\Gamma(\nu/2)} \left(1 + \frac{x^2}{\nu}\right)^{-(\nu+1)/2}$$

**Key**: Heavier tails than Gaussian—more robust to outliers!

```python
def student_t_distribution():
    """Student's t: robust alternative to Gaussian."""
    
    x = np.linspace(-5, 5, 1000)
    
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    
    # Comparison with normal
    ax = axes[0]
    ax.plot(x, stats.norm.pdf(x), 'b-', linewidth=2, label='Normal (ν=∞)')
    for df in [1, 3, 10]:
        ax.plot(x, stats.t.pdf(x, df=df), label=f't (ν={df})')
    ax.set_xlabel('x')
    ax.set_ylabel('f(x)')
    ax.set_title('Student\'s t vs Normal: Heavy Tails')
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    # Robust regression example
    ax = axes[1]
    np.random.seed(42)
    n = 50
    x_data = np.random.uniform(0, 10, n)
    y_true = 2 * x_data + 1
    y_noisy = y_true + np.random.normal(0, 1, n)
    
    # Add outliers
    outlier_idx = [0, 10, 20]
    y_noisy[outlier_idx] += np.array([10, -8, 12])
    
    # OLS fit (Gaussian assumption)
    m_ols, b_ols = np.polyfit(x_data, y_noisy, 1)
    
    ax.scatter(x_data, y_noisy, alpha=0.6)
    ax.scatter(x_data[outlier_idx], y_noisy[outlier_idx], color='red', s=100, label='Outliers')
    ax.plot(x_data, 2*x_data + 1, 'g-', linewidth=2, label='True: y=2x+1')
    ax.plot(x_data, m_ols*x_data + b_ols, 'b--', linewidth=2, 
            label=f'OLS: y={m_ols:.2f}x+{b_ols:.2f}')
    ax.set_xlabel('x')
    ax.set_ylabel('y')
    ax.set_title('OLS is Sensitive to Outliers (Gaussian assumption)')
    ax.legend()
    
    plt.tight_layout()
    plt.show()
    
    print("For robust regression, use t-distribution likelihood instead of Gaussian!")

student_t_distribution()
```

---

## Multivariate Distributions

### Multivariate Gaussian

$$f(\mathbf{x}) = \frac{1}{(2\pi)^{d/2}|\Sigma|^{1/2}} \exp\left(-\frac{1}{2}(\mathbf{x}-\boldsymbol{\mu})^T \Sigma^{-1} (\mathbf{x}-\boldsymbol{\mu})\right)$$

**Parameters**: Mean vector $\boldsymbol{\mu}$, Covariance matrix $\Sigma$

```python
def multivariate_gaussian():
    """Multivariate Gaussian visualization."""
    
    # 2D Gaussian
    mean = [0, 0]
    
    fig, axes = plt.subplots(1, 3, figsize=(15, 5))
    
    covariances = [
        [[1, 0], [0, 1]],      # Independent
        [[1, 0.8], [0.8, 1]],  # Positive correlation
        [[2, -0.5], [-0.5, 0.5]]  # Different variances + negative correlation
    ]
    titles = ['Independent (ρ=0)', 'Correlated (ρ=0.8)', 'Different scales']
    
    x = np.linspace(-3, 3, 100)
    y = np.linspace(-3, 3, 100)
    X, Y = np.meshgrid(x, y)
    pos = np.dstack((X, Y))
    
    for ax, cov, title in zip(axes, covariances, titles):
        rv = stats.multivariate_normal(mean, cov)
        Z = rv.pdf(pos)
        
        ax.contourf(X, Y, Z, levels=20, cmap='viridis')
        ax.contour(X, Y, Z, levels=10, colors='white', alpha=0.5)
        ax.set_xlabel('x₁')
        ax.set_ylabel('x₂')
        ax.set_title(title)
        ax.set_aspect('equal')
    
    plt.tight_layout()
    plt.show()

multivariate_gaussian()
```

### Dirichlet Distribution

**When to use**: Prior over probability simplex (topic models, mixture weights)

$$f(\mathbf{x}; \boldsymbol{\alpha}) = \frac{\Gamma(\sum_k \alpha_k)}{\prod_k \Gamma(\alpha_k)} \prod_k x_k^{\alpha_k - 1}$$

**Constraint**: $\sum_k x_k = 1$, $x_k > 0$

```python
def dirichlet_distribution():
    """Dirichlet: distribution over probability vectors."""
    
    # Visualize on 2-simplex (3 categories)
    def draw_simplex_samples(alpha, n_samples=500, ax=None, title=''):
        samples = np.random.dirichlet(alpha, n_samples)
        
        # Convert to 2D coordinates on simplex
        # Using barycentric coordinates
        corners = np.array([[0, 0], [1, 0], [0.5, np.sqrt(3)/2]])
        xy = samples @ corners
        
        if ax is None:
            fig, ax = plt.subplots(figsize=(6, 5))
        
        ax.scatter(xy[:, 0], xy[:, 1], alpha=0.3, s=10)
        
        # Draw simplex triangle
        triangle = plt.Polygon(corners, fill=False, edgecolor='black')
        ax.add_patch(triangle)
        
        ax.set_xlim(-0.1, 1.1)
        ax.set_ylim(-0.1, 1.0)
        ax.set_aspect('equal')
        ax.set_title(f'{title}\nα = {alpha}')
        ax.axis('off')
        
        return ax
    
    fig, axes = plt.subplots(2, 3, figsize=(15, 10))
    
    alphas = [
        ([1, 1, 1], 'Uniform'),
        ([0.1, 0.1, 0.1], 'Sparse (corners)'),
        ([10, 10, 10], 'Concentrated (center)'),
        ([5, 1, 1], 'Favor category 1'),
        ([1, 5, 1], 'Favor category 2'),
        ([1, 1, 5], 'Favor category 3'),
    ]
    
    for ax, (alpha, title) in zip(axes.flat, alphas):
        draw_simplex_samples(alpha, ax=ax, title=title)
    
    plt.suptitle('Dirichlet Distribution on 2-Simplex', fontsize=14)
    plt.tight_layout()
    plt.show()

dirichlet_distribution()
```

---

## Distribution Selection Guide

### Quick Reference Table

| Scenario | Distribution | Parameters |
|----------|-------------|------------|
| Binary outcome | Bernoulli | $p$ |
| Count (n trials) | Binomial | $n, p$ |
| Count (unbounded) | Poisson | $\lambda$ |
| Multi-class | Categorical | $\mathbf{p}$ |
| Time to event | Exponential | $\lambda$ |
| Waiting time (k events) | Gamma | $k, \theta$ |
| Continuous (unbounded) | Gaussian | $\mu, \sigma^2$ |
| Bounded [0,1] | Beta | $\alpha, \beta$ |
| Heavy tails | Student's t | $\nu$ |
| Probability simplex | Dirichlet | $\boldsymbol{\alpha}$ |

### Decision Flowchart

```
Is your variable discrete or continuous?
│
├── DISCRETE
│   ├── Binary? → Bernoulli
│   ├── Fixed trials? → Binomial
│   ├── Multiple categories? → Categorical/Multinomial
│   └── Count (rare events)? → Poisson
│
└── CONTINUOUS
    ├── Bounded [0,1]? → Beta
    ├── Positive only? → Exponential/Gamma
    ├── Heavy tails needed? → Student's t
    ├── Multivariate? → Multivariate Gaussian
    └── Default → Gaussian
```

---

## Testing Distributions

### Goodness-of-Fit Tests

```python
def distribution_testing():
    """Test if data follows a specific distribution."""
    
    np.random.seed(42)
    
    # Generate data
    n = 500
    normal_data = np.random.normal(0, 1, n)
    uniform_data = np.random.uniform(-2, 2, n)
    exponential_data = np.random.exponential(1, n)
    
    datasets = {
        'Normal data': normal_data,
        'Uniform data': uniform_data,
        'Exponential data': exponential_data
    }
    
    print("Normality Tests")
    print("=" * 60)
    
    for name, data in datasets.items():
        # Shapiro-Wilk test (best for n < 5000)
        stat, p = stats.shapiro(data[:500])  # Limited to 5000
        
        # Anderson-Darling test
        result = stats.anderson(data, dist='norm')
        
        print(f"\n{name}:")
        print(f"  Shapiro-Wilk: stat={stat:.4f}, p={p:.4f}")
        print(f"  {'Likely Normal' if p > 0.05 else 'NOT Normal'} (α=0.05)")
        
    # Visual: Q-Q plots
    fig, axes = plt.subplots(1, 3, figsize=(15, 4))
    
    for ax, (name, data) in zip(axes, datasets.items()):
        stats.probplot(data, dist="norm", plot=ax)
        ax.set_title(f'Q-Q Plot: {name}')
    
    plt.tight_layout()
    plt.show()

distribution_testing()
```

---

## Distributions in PyTorch/TensorFlow

### PyTorch Distributions

```python
# PyTorch distributions module
import torch
import torch.distributions as dist

def pytorch_distributions_demo():
    """Using PyTorch distributions."""
    
    # Gaussian
    normal = dist.Normal(loc=0.0, scale=1.0)
    samples = normal.sample((1000,))
    log_prob = normal.log_prob(torch.tensor([0.0, 1.0, 2.0]))
    
    print("PyTorch Distributions")
    print("=" * 50)
    print(f"Normal(0,1) samples mean: {samples.mean():.4f}")
    print(f"Log prob at [0, 1, 2]: {log_prob}")
    
    # Categorical (for classification)
    probs = torch.tensor([0.2, 0.5, 0.3])
    cat = dist.Categorical(probs=probs)
    samples = cat.sample((100,))
    print(f"\nCategorical samples: {[int((samples==i).sum()) for i in range(3)]}")
    
    # Reparameterization trick (VAE)
    mu = torch.tensor([1.0, 2.0])
    sigma = torch.tensor([0.5, 0.3])
    
    # Standard way (non-differentiable through sampling)
    # z = dist.Normal(mu, sigma).sample()
    
    # Reparameterization (differentiable!)
    eps = torch.randn_like(mu)
    z_reparam = mu + sigma * eps
    print(f"\nReparameterized sample: {z_reparam}")

# pytorch_distributions_demo()  # Uncomment if PyTorch available
```

### TensorFlow Probability

```python
# TensorFlow Probability example
# import tensorflow_probability as tfp
# tfd = tfp.distributions

def tfp_example():
    """TensorFlow Probability distributions."""
    
    print("TensorFlow Probability")
    print("=" * 50)
    print("""
    # Gaussian
    normal = tfd.Normal(loc=0., scale=1.)
    
    # Mixture model
    mixture = tfd.MixtureSameFamily(
        mixture_distribution=tfd.Categorical(probs=[0.3, 0.7]),
        components_distribution=tfd.Normal(
            loc=[-1., 1.],
            scale=[0.5, 0.5])
    )
    
    # Variational inference
    surrogate_posterior = tfd.MultivariateNormalDiag(
        loc=tf.Variable(tf.zeros(10)),
        scale_diag=tf.nn.softplus(tf.Variable(tf.ones(10)))
    )
    """)

tfp_example()
```

---

## FAQs

### How do I know which distribution to use?

1. **Look at your data**: Plot histograms, check bounds
2. **Consider the domain**: Counts → Poisson/Binomial; Probabilities → Beta
3. **Check constraints**: Positive only? Bounded? Sum to 1?
4. **Test assumptions**: Use goodness-of-fit tests

### Why is Gaussian so popular in ML?

- **Central Limit Theorem**: Sum of many variables → Gaussian
- **Maximum entropy**: Most "uncertain" distribution for given mean/variance
- **Analytical tractability**: Easy math
- **Often good enough**: Real-world data is approximately Gaussian

### What's the connection between distributions and loss functions?

| Distribution | Loss Function |
|--------------|---------------|
| Bernoulli | Binary Cross-Entropy |
| Categorical | Cross-Entropy |
| Gaussian | MSE (Mean Squared Error) |
| Laplace | MAE (Mean Absolute Error) |
| Poisson | Poisson Loss |

---

## Key Takeaways

1. **Loss functions come from distributions**: Cross-entropy from Categorical, MSE from Gaussian
2. **Bernoulli → Sigmoid**, **Categorical → Softmax** in neural networks
3. **Beta and Dirichlet** are priors for probabilities in Bayesian ML
4. **Test your assumptions**: Use Q-Q plots and statistical tests
5. **Heavy tails? Use Student's t** for robustness

---

## Next Steps

Continue your probability journey:

1. **[Bayes' Theorem Explained](/docs/ai-ml/foundations/mathematics/probability/bayes-theorem-explained/)** - Bayesian inference
2. **[MLE vs MAP](/docs/ai-ml/foundations/mathematics/probability/mle-vs-map/)** - Parameter estimation
3. **[Information Theory](/docs/ai-ml/foundations/mathematics/probability/information-theory-entropy/)** - Entropy and KL divergence

---

## References

1. Bishop, C. M. "Pattern Recognition and Machine Learning" - Chapter 2
2. Murphy, K. P. "Machine Learning: A Probabilistic Perspective" - Chapters 2-3
3. Wasserman, L. "All of Statistics" - Chapters 2-4
4. SciPy Documentation - scipy.stats module

---

*Last updated: January 2024. Part of our [Mathematics for Machine Learning](/docs/ai-ml/foundations/mathematics/) series.*
