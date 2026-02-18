---
title: "MLE vs MAP: Maximum Likelihood and Bayesian Parameter Estimation"
description: "Master MLE and MAP estimation for machine learning. Learn when to use each, mathematical foundations, conjugate priors, and Python implementations."
date: 2024-01-15
lastmod: 2024-01-15
draft: false
weight: 3
toc: true
categories: ["AI/ML", "Mathematics", "Statistics"]
tags: ["MLE", "MAP", "maximum likelihood", "bayesian estimation", "parameter estimation"]
series: ["Mathematics for ML"]
---

# MLE vs MAP: Maximum Likelihood and Bayesian Parameter Estimation

**"MLE asks: 'What parameters best explain the data?' MAP asks: 'What parameters best explain the data AND are plausible?'"**

Parameter estimation is fundamental to machine learning. Every model has parameters that need to be learned from data. This guide covers the two most important estimation methods: **Maximum Likelihood Estimation (MLE)** and **Maximum A Posteriori (MAP)** estimation.

---

## The Big Picture

### Two Philosophies

```
MLE (Frequentist):
    θ* = argmax P(Data | θ)
         θ
    "Find parameters that make data most likely"

MAP (Bayesian):
    θ* = argmax P(θ | Data) = argmax P(Data | θ) × P(θ)
         θ                    θ
    "Find parameters that are most probable given data AND prior"
```

### Visual Intuition

```
                         Prior P(θ)
                            │
                            ▼
    ┌─────────────────────────────────────────┐
    │         ╱╲                              │
    │        ╱  ╲                             │
    │       ╱    ╲                            │
    │      ╱      ╲                           │
    │     ╱        ╲                          │
    │    ╱──────────╲─────────────────────────│
    └─────────────────────────────────────────┘
                            │
                            │ × Likelihood P(D|θ)
                            ▼
    ┌─────────────────────────────────────────┐
    │               ╱╲                        │  MLE: Peak of Likelihood
    │              ╱  ╲      ╱╲               │  MAP: Peak of Posterior
    │             ╱    ╲    ╱  ╲              │       (shifted by prior)
    │────────────╱──────╲──╱────╲─────────────│
    └─────────────────────────────────────────┘
                     │
                     ▼
            Posterior P(θ|D)
```

---

## Maximum Likelihood Estimation (MLE)

### The Core Idea

Given data $D = \{x_1, x_2, ..., x_n\}$, find parameters $\theta$ that maximize the probability of observing this data:

$$\theta_{MLE} = \argmax_\theta P(D | \theta) = \argmax_\theta \prod_{i=1}^{n} P(x_i | \theta)$$

### Log-Likelihood (Numerical Stability)

Products of small probabilities → numerical underflow. Solution: use log-likelihood.

$$\theta_{MLE} = \argmax_\theta \sum_{i=1}^{n} \log P(x_i | \theta)$$

```python
import numpy as np
from scipy import stats
import matplotlib.pyplot as plt

def mle_gaussian_example():
    """MLE for Gaussian distribution parameters."""
    
    # Generate data from unknown Gaussian
    np.random.seed(42)
    true_mu, true_sigma = 5.0, 2.0
    data = np.random.normal(true_mu, true_sigma, 100)
    
    # MLE estimates
    mu_mle = data.mean()
    sigma_mle = data.std()  # Note: NumPy uses n, MLE uses n (biased)
    sigma_mle_unbiased = data.std(ddof=1)  # n-1 (unbiased)
    
    print("MLE for Gaussian Parameters")
    print("=" * 50)
    print(f"True parameters: μ = {true_mu}, σ = {true_sigma}")
    print(f"MLE estimates:   μ = {mu_mle:.3f}, σ = {sigma_mle:.3f}")
    print(f"Unbiased σ:      σ = {sigma_mle_unbiased:.3f}")
    
    # Visualize likelihood surface
    mu_range = np.linspace(3, 7, 100)
    sigma_range = np.linspace(1, 3, 100)
    MU, SIGMA = np.meshgrid(mu_range, sigma_range)
    
    # Log-likelihood
    log_likelihood = np.zeros_like(MU)
    for i, (mu, sigma) in enumerate(zip(MU.flat, SIGMA.flat)):
        log_likelihood.flat[i] = stats.norm.logpdf(data, mu, sigma).sum()
    
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    
    # Contour plot
    ax = axes[0]
    contour = ax.contourf(MU, SIGMA, log_likelihood, levels=30, cmap='viridis')
    ax.scatter([mu_mle], [sigma_mle], color='red', s=100, marker='*', 
               label=f'MLE: ({mu_mle:.2f}, {sigma_mle:.2f})')
    ax.scatter([true_mu], [true_sigma], color='white', s=100, marker='o',
               label=f'True: ({true_mu}, {true_sigma})')
    ax.set_xlabel('μ')
    ax.set_ylabel('σ')
    ax.set_title('Log-Likelihood Surface')
    ax.legend()
    plt.colorbar(contour, ax=ax, label='Log-likelihood')
    
    # Data with fitted distribution
    ax = axes[1]
    ax.hist(data, bins=20, density=True, alpha=0.7, label='Data')
    x = np.linspace(data.min()-1, data.max()+1, 100)
    ax.plot(x, stats.norm.pdf(x, mu_mle, sigma_mle), 'r-', linewidth=2,
            label=f'MLE fit: N({mu_mle:.2f}, {sigma_mle:.2f}²)')
    ax.plot(x, stats.norm.pdf(x, true_mu, true_sigma), 'g--', linewidth=2,
            label=f'True: N({true_mu}, {true_sigma}²)')
    ax.set_xlabel('x')
    ax.set_ylabel('Density')
    ax.set_title('Data and MLE Fit')
    ax.legend()
    
    plt.tight_layout()
    plt.show()

mle_gaussian_example()
```

### Deriving MLE for Gaussian

For Gaussian $X \sim N(\mu, \sigma^2)$:

$$\log P(x_i | \mu, \sigma) = -\frac{1}{2}\log(2\pi\sigma^2) - \frac{(x_i - \mu)^2}{2\sigma^2}$$

**For μ**:
$$\frac{\partial}{\partial \mu} \sum_i \log P(x_i) = \sum_i \frac{x_i - \mu}{\sigma^2} = 0$$
$$\Rightarrow \mu_{MLE} = \frac{1}{n}\sum_i x_i = \bar{x}$$

**For σ²**:
$$\sigma^2_{MLE} = \frac{1}{n}\sum_i (x_i - \bar{x})^2$$

```python
def derive_mle_analytically():
    """Show MLE derivation for common distributions."""
    
    print("MLE Formulas for Common Distributions")
    print("=" * 60)
    
    formulas = [
        ("Bernoulli(p)", "p_MLE = (# successes) / n"),
        ("Gaussian(μ, σ²)", "μ_MLE = mean(x), σ²_MLE = (1/n)Σ(x - μ)²"),
        ("Poisson(λ)", "λ_MLE = mean(x)"),
        ("Exponential(λ)", "λ_MLE = 1 / mean(x)"),
        ("Uniform(a, b)", "a_MLE = min(x), b_MLE = max(x)"),
    ]
    
    for dist, formula in formulas:
        print(f"{dist:20s}: {formula}")

derive_mle_analytically()
```

### MLE and Loss Functions

**Key insight**: Minimizing loss functions = Maximizing likelihood!

| Distribution Assumption | Loss Function | MLE Connection |
|------------------------|---------------|----------------|
| Gaussian (regression) | MSE | σ² doesn't affect argmax |
| Bernoulli (binary) | Binary Cross-Entropy | Sigmoid output |
| Categorical (multi-class) | Cross-Entropy | Softmax output |
| Laplace | MAE (L1) | Median instead of mean |

```python
def loss_as_negative_log_likelihood():
    """Show loss functions as negative log-likelihood."""
    
    print("Loss Functions = Negative Log-Likelihood")
    print("=" * 60)
    
    # Binary Cross-Entropy
    print("\n1. Binary Classification (Bernoulli)")
    print("   P(y|x) = p^y × (1-p)^(1-y)")
    print("   -log P(y|x) = -y×log(p) - (1-y)×log(1-p)")
    print("   = Binary Cross-Entropy!")
    
    # MSE
    print("\n2. Regression (Gaussian)")
    print("   P(y|x) ∝ exp(-(y-μ)²/2σ²)")
    print("   -log P(y|x) ∝ (y-μ)²")
    print("   = Mean Squared Error!")
    
    # Numerical example
    y_true = np.array([1, 0, 1, 1])
    y_pred = np.array([0.9, 0.1, 0.8, 0.7])
    
    # BCE manual
    bce_manual = -np.mean(y_true * np.log(y_pred) + (1-y_true) * np.log(1-y_pred))
    print(f"\n   Example BCE: {bce_manual:.4f}")

loss_as_negative_log_likelihood()
```

---

## Maximum A Posteriori (MAP)

### Adding the Prior

MAP includes a prior distribution over parameters:

$$\theta_{MAP} = \argmax_\theta P(\theta | D) = \argmax_\theta P(D | \theta) P(\theta)$$

In log form:
$$\theta_{MAP} = \argmax_\theta \left[\log P(D | \theta) + \log P(\theta)\right]$$

### The Regularization Connection

**Gaussian prior** → **L2 regularization (Ridge)**
$$P(\theta) = N(0, \sigma^2) \Rightarrow \log P(\theta) = -\frac{\theta^2}{2\sigma^2} + const$$

**Laplace prior** → **L1 regularization (Lasso)**
$$P(\theta) = Laplace(0, b) \Rightarrow \log P(\theta) = -\frac{|\theta|}{b} + const$$

```python
def map_vs_mle_demo():
    """Demonstrate MAP vs MLE with regularization."""
    
    # Generate sparse data
    np.random.seed(42)
    n_samples = 20
    n_features = 10
    
    # True weights: sparse
    true_weights = np.array([3, -2, 0, 0, 0, 1.5, 0, 0, 0, 0])
    
    X = np.random.randn(n_samples, n_features)
    y = X @ true_weights + np.random.randn(n_samples) * 0.5
    
    # MLE (OLS)
    w_mle = np.linalg.lstsq(X, y, rcond=None)[0]
    
    # MAP with Gaussian prior (Ridge = L2)
    lambda_l2 = 1.0
    w_map_l2 = np.linalg.solve(X.T @ X + lambda_l2 * np.eye(n_features), X.T @ y)
    
    # MAP with Laplace prior (Lasso = L1) - using sklearn
    from sklearn.linear_model import Lasso
    lasso = Lasso(alpha=0.1)
    lasso.fit(X, y)
    w_map_l1 = lasso.coef_
    
    # Compare
    fig, ax = plt.subplots(figsize=(12, 6))
    
    x_pos = np.arange(n_features)
    width = 0.2
    
    ax.bar(x_pos - 1.5*width, true_weights, width, label='True', color='green', alpha=0.7)
    ax.bar(x_pos - 0.5*width, w_mle, width, label='MLE (OLS)', color='blue', alpha=0.7)
    ax.bar(x_pos + 0.5*width, w_map_l2, width, label='MAP + Gaussian (Ridge)', color='orange', alpha=0.7)
    ax.bar(x_pos + 1.5*width, w_map_l1, width, label='MAP + Laplace (Lasso)', color='red', alpha=0.7)
    
    ax.set_xlabel('Feature Index')
    ax.set_ylabel('Weight')
    ax.set_title('MLE vs MAP: Effect of Priors on Parameter Estimation')
    ax.set_xticks(x_pos)
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    plt.tight_layout()
    plt.show()
    
    print("Weight Estimation Comparison")
    print("=" * 60)
    print(f"True weights:      {true_weights.round(2)}")
    print(f"MLE:               {w_mle.round(2)}")
    print(f"MAP (L2/Ridge):    {w_map_l2.round(2)}")
    print(f"MAP (L1/Lasso):    {w_map_l1.round(2)}")
    
    # MSE on weights
    print(f"\nWeight MSE:")
    print(f"  MLE:       {np.mean((w_mle - true_weights)**2):.4f}")
    print(f"  MAP (L2):  {np.mean((w_map_l2 - true_weights)**2):.4f}")
    print(f"  MAP (L1):  {np.mean((w_map_l1 - true_weights)**2):.4f}")

map_vs_mle_demo()
```

### Mathematical Connection: Regularization = Prior

```
Loss Function              = -log P(D|θ) + λ × Regularization
                          = -log P(D|θ) - log P(θ)   (with prior)
                          = -log P(θ|D)              (posterior)

Therefore: Minimizing Regularized Loss = Maximizing Posterior = MAP!
```

| Regularization | Prior | Formula |
|----------------|-------|---------|
| L2 (Ridge) | Gaussian | $P(\theta) \propto e^{-\theta^2/2\sigma^2}$ |
| L1 (Lasso) | Laplace | $P(\theta) \propto e^{-|\theta|/b}$ |
| Elastic Net | Gaussian + Laplace mix | Combined |

---

## Coin Flip Example: MLE vs MAP

```python
def coin_flip_comparison():
    """Compare MLE and MAP for coin flip probability."""
    
    # Observed: 3 heads in 3 flips
    n_heads = 3
    n_flips = 3
    
    p = np.linspace(0.001, 0.999, 1000)
    
    # Likelihood
    likelihood = stats.binom.pmf(n_heads, n_flips, p)
    
    # MLE
    p_mle = n_heads / n_flips  # = 1.0
    
    # Different priors
    priors = {
        'Uniform (MLE)': stats.beta.pdf(p, 1, 1),
        'Weak (Beta(2,2))': stats.beta.pdf(p, 2, 2),
        'Strong (Beta(10,10))': stats.beta.pdf(p, 10, 10),
    }
    
    fig, axes = plt.subplots(1, 3, figsize=(15, 5))
    
    for ax, (name, prior) in zip(axes, priors.items()):
        # Posterior ∝ Likelihood × Prior
        posterior = likelihood * prior
        posterior /= posterior.sum() * (p[1] - p[0])  # Normalize
        
        ax.plot(p, prior / prior.max(), 'g--', label='Prior', linewidth=2)
        ax.plot(p, likelihood / likelihood.max(), 'b:', label='Likelihood', linewidth=2)
        ax.plot(p, posterior / posterior.max(), 'r-', label='Posterior', linewidth=2)
        
        # MAP estimate
        p_map = p[np.argmax(posterior)]
        ax.axvline(p_map, color='red', linestyle='-.', alpha=0.7,
                  label=f'MAP = {p_map:.2f}')
        ax.axvline(p_mle, color='blue', linestyle='-.', alpha=0.7,
                  label=f'MLE = {p_mle:.2f}')
        
        ax.set_xlabel('p (probability of heads)')
        ax.set_ylabel('Normalized Density')
        ax.set_title(f'Prior: {name}')
        ax.legend()
        ax.grid(True, alpha=0.3)
    
    plt.suptitle('MLE vs MAP: Effect of Prior (3 heads in 3 flips)', fontsize=14)
    plt.tight_layout()
    plt.show()
    
    print("Coin Flip Analysis: 3 heads in 3 flips")
    print("=" * 50)
    print(f"MLE (no prior): p = {p_mle:.2f}")
    print("   Problem: Says coin always lands heads!")
    print("\nMAP with different priors:")
    print("   Uniform:     p ≈ 1.00 (same as MLE)")
    print("   Beta(2,2):   p ≈ 0.80 (pulled toward 0.5)")
    print("   Beta(10,10): p ≈ 0.60 (strongly pulled toward 0.5)")

coin_flip_comparison()
```

---

## When to Use MLE vs MAP

### Decision Guide

```
                    ┌─────────────────────────────────┐
                    │ Do you have informative prior?  │
                    └─────────────┬───────────────────┘
                                  │
                    ┌─────────────┴───────────────┐
                    │                             │
                   Yes                           No
                    │                             │
                    ▼                             ▼
            ┌───────────────┐            ┌───────────────┐
            │ Use MAP       │            │ Large dataset?│
            │ (incorporate  │            └───────┬───────┘
            │  prior info)  │                    │
            └───────────────┘          ┌─────────┴─────────┐
                                       │                   │
                                      Yes                  No
                                       │                   │
                                       ▼                   ▼
                              ┌───────────────┐   ┌───────────────┐
                              │ Use MLE       │   │ Use MAP with  │
                              │ (prior won't  │   │ weak prior    │
                              │  matter much) │   │ (regularize)  │
                              └───────────────┘   └───────────────┘
```

### Comparison Table

| Aspect | MLE | MAP |
|--------|-----|-----|
| Prior | Not used | Explicitly included |
| Overfitting | More prone | Regularized |
| Small data | Can overfit | Prior helps |
| Large data | Both converge | Prior becomes irrelevant |
| Computation | Often simpler | May need optimization |
| Interpretation | "Most likely parameters" | "Most probable parameters" |

```python
def convergence_with_data():
    """Show MLE and MAP converge with more data."""
    
    true_p = 0.6
    
    data_sizes = [5, 10, 20, 50, 100, 500]
    
    fig, axes = plt.subplots(2, 3, figsize=(15, 10))
    p = np.linspace(0.001, 0.999, 1000)
    
    # Strong prior: Beta(10, 10)
    alpha_prior, beta_prior = 10, 10
    
    np.random.seed(42)
    
    for ax, n in zip(axes.flat, data_sizes):
        # Generate data
        flips = np.random.binomial(1, true_p, n)
        n_heads = flips.sum()
        
        # Likelihood
        likelihood = stats.binom.pmf(n_heads, n, p)
        
        # Prior
        prior = stats.beta.pdf(p, alpha_prior, beta_prior)
        
        # Posterior
        alpha_post = alpha_prior + n_heads
        beta_post = beta_prior + (n - n_heads)
        posterior = stats.beta.pdf(p, alpha_post, beta_post)
        
        # Estimates
        p_mle = n_heads / n
        p_map = (alpha_post - 1) / (alpha_post + beta_post - 2)  # Mode of Beta
        
        ax.plot(p, prior / prior.max(), 'g--', label='Prior', linewidth=2)
        ax.plot(p, posterior / posterior.max(), 'r-', label='Posterior', linewidth=2)
        ax.axvline(p_mle, color='blue', linestyle=':', label=f'MLE={p_mle:.2f}')
        ax.axvline(p_map, color='red', linestyle='-.', label=f'MAP={p_map:.2f}')
        ax.axvline(true_p, color='green', linestyle='-', label=f'True={true_p}')
        
        ax.set_xlabel('p')
        ax.set_title(f'n = {n} samples')
        ax.legend(fontsize=8)
        ax.grid(True, alpha=0.3)
    
    plt.suptitle('MLE and MAP Converge with More Data (True p = 0.6)', fontsize=14)
    plt.tight_layout()
    plt.show()
    
    print("Key insight: With enough data, prior becomes irrelevant!")
    print("MLE and MAP give essentially the same answer for large n.")

convergence_with_data()
```

---

## MLE/MAP in Deep Learning

### Weight Initialization

```python
def weight_initialization_priors():
    """Weight initialization as implicit prior."""
    
    print("Weight Initialization = Implicit Prior")
    print("=" * 50)
    
    initializations = {
        'Zero init': 'P(w) = δ(w) — all weights start at 0',
        'Xavier/Glorot': 'P(w) = N(0, 2/(n_in + n_out)) — Gaussian prior',
        'He init': 'P(w) = N(0, 2/n_in) — for ReLU',
        'Orthogonal': 'P(W) uniform on orthogonal matrices',
    }
    
    for name, prior in initializations.items():
        print(f"{name:15s}: {prior}")
    
    # Visualize
    fig, ax = plt.subplots(figsize=(10, 5))
    
    n_in, n_out = 784, 256
    
    # Different initializations
    np.random.seed(42)
    
    inits = {
        'Xavier': np.random.randn(1000) * np.sqrt(2 / (n_in + n_out)),
        'He': np.random.randn(1000) * np.sqrt(2 / n_in),
        'LeCun': np.random.randn(1000) * np.sqrt(1 / n_in),
    }
    
    for name, weights in inits.items():
        ax.hist(weights, bins=50, alpha=0.5, label=f'{name}: σ={weights.std():.4f}')
    
    ax.set_xlabel('Weight Value')
    ax.set_ylabel('Count')
    ax.set_title('Weight Initialization Distributions (n_in=784, n_out=256)')
    ax.legend()
    
    plt.tight_layout()
    plt.show()

weight_initialization_priors()
```

### Regularization as MAP

```python
def nn_regularization_as_map():
    """Show neural network regularization as MAP."""
    
    print("Neural Network Regularization = MAP Estimation")
    print("=" * 60)
    
    print("""
    Standard Loss:
    L(θ) = Σᵢ ℓ(yᵢ, f(xᵢ; θ))
    
    This is MLE (maximizing likelihood = minimizing negative log-likelihood)
    
    With L2 Regularization:
    L(θ) = Σᵢ ℓ(yᵢ, f(xᵢ; θ)) + λ||θ||²
    
    This is MAP with Gaussian prior P(θ) = N(0, 1/(2λ))!
    
    With L1 Regularization:
    L(θ) = Σᵢ ℓ(yᵢ, f(xᵢ; θ)) + λ||θ||₁
    
    This is MAP with Laplace prior!
    
    Weight Decay in SGD:
    θ ← θ - lr × (∇L + λθ)
    
    Equivalent to L2 regularization!
    """)
    
    # PyTorch example
    print("\nPyTorch Implementation:")
    print("""
    # L2 regularization via weight_decay
    optimizer = torch.optim.SGD(model.parameters(), 
                                lr=0.01, 
                                weight_decay=0.01)  # This is λ!
    
    # Or manually:
    loss = criterion(output, target)
    l2_reg = sum(p.pow(2).sum() for p in model.parameters())
    loss = loss + lambda_l2 * l2_reg
    """)

nn_regularization_as_map()
```

### Dropout as Approximate Bayesian Inference

```python
def dropout_as_bayesian():
    """Dropout as approximate Bayesian inference."""
    
    print("Dropout ≈ Bayesian Neural Network")
    print("=" * 50)
    
    print("""
    Key insight (Gal & Ghahramani, 2016):
    
    Training with dropout is equivalent to approximate 
    variational inference in a deep Gaussian process!
    
    At test time:
    - Standard: Disable dropout, single prediction
    - Bayesian: Keep dropout ON, multiple predictions
              → Get uncertainty estimates!
    
    Monte Carlo Dropout:
    1. Keep dropout active at test time
    2. Run N forward passes
    3. Mean = prediction
    4. Variance = uncertainty
    """)
    
    # Simulate MC Dropout
    np.random.seed(42)
    
    # Simulate predictions with dropout
    n_samples = 100
    true_prediction = 5.0
    dropout_predictions = true_prediction + np.random.randn(n_samples) * 0.5
    
    mean_pred = dropout_predictions.mean()
    std_pred = dropout_predictions.std()
    
    fig, ax = plt.subplots(figsize=(10, 5))
    
    ax.hist(dropout_predictions, bins=20, density=True, alpha=0.7)
    ax.axvline(mean_pred, color='red', linestyle='--', 
               label=f'Mean: {mean_pred:.2f}')
    ax.axvspan(mean_pred - 2*std_pred, mean_pred + 2*std_pred,
               alpha=0.2, color='red', label=f'95% CI: [{mean_pred-2*std_pred:.2f}, {mean_pred+2*std_pred:.2f}]')
    
    ax.set_xlabel('Prediction')
    ax.set_ylabel('Density')
    ax.set_title('Monte Carlo Dropout: Uncertainty Estimation')
    ax.legend()
    
    plt.tight_layout()
    plt.show()

dropout_as_bayesian()
```

---

## Practical Implementation

### Complete Example: Linear Regression

```python
def complete_linear_regression_example():
    """Complete MLE vs MAP for linear regression."""
    
    # Generate data
    np.random.seed(42)
    n = 50
    X = np.random.randn(n, 5)
    true_w = np.array([2, -1, 0.5, 0, 0])
    y = X @ true_w + np.random.randn(n) * 0.5
    
    # Add column of ones for intercept
    X_with_intercept = np.column_stack([np.ones(n), X])
    
    # MLE (OLS)
    w_mle = np.linalg.lstsq(X_with_intercept, y, rcond=None)[0]
    
    # MAP with different priors
    def map_ridge(X, y, lambda_):
        """MAP with Gaussian prior (Ridge)."""
        n_features = X.shape[1]
        return np.linalg.solve(
            X.T @ X + lambda_ * np.eye(n_features),
            X.T @ y
        )
    
    w_map_weak = map_ridge(X_with_intercept, y, 0.1)
    w_map_strong = map_ridge(X_with_intercept, y, 10.0)
    
    print("Linear Regression: MLE vs MAP")
    print("=" * 60)
    print(f"True weights:        {np.array([0] + list(true_w)).round(2)}")  # Include intercept
    print(f"MLE:                 {w_mle.round(2)}")
    print(f"MAP (λ=0.1):         {w_map_weak.round(2)}")
    print(f"MAP (λ=10):          {w_map_strong.round(2)}")
    
    # Prediction comparison
    X_test = np.random.randn(10, 5)
    X_test_with_intercept = np.column_stack([np.ones(10), X_test])
    y_test_true = X_test @ true_w
    
    predictions = {
        'MLE': X_test_with_intercept @ w_mle,
        'MAP (λ=0.1)': X_test_with_intercept @ w_map_weak,
        'MAP (λ=10)': X_test_with_intercept @ w_map_strong,
    }
    
    print("\nTest MSE:")
    for name, pred in predictions.items():
        mse = np.mean((pred - y_test_true) ** 2)
        print(f"  {name:15s}: {mse:.4f}")

complete_linear_regression_example()
```

### Logistic Regression: MLE vs MAP

```python
def logistic_regression_mle_map():
    """Logistic regression with MLE and MAP."""
    
    from sklearn.linear_model import LogisticRegression
    from sklearn.datasets import make_classification
    from sklearn.model_selection import train_test_split
    
    # Generate data (small dataset to show regularization effect)
    X, y = make_classification(n_samples=100, n_features=20, 
                               n_informative=5, n_redundant=5,
                               random_state=42)
    
    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.3, random_state=42
    )
    
    # Different regularization strengths
    models = {
        'MLE (no reg)': LogisticRegression(penalty=None, solver='lbfgs', max_iter=1000),
        'MAP (weak L2)': LogisticRegression(penalty='l2', C=10, solver='lbfgs'),
        'MAP (strong L2)': LogisticRegression(penalty='l2', C=0.1, solver='lbfgs'),
        'MAP (L1/Lasso)': LogisticRegression(penalty='l1', C=1, solver='saga'),
    }
    
    print("Logistic Regression: MLE vs MAP")
    print("=" * 60)
    
    results = []
    for name, model in models.items():
        model.fit(X_train, y_train)
        train_acc = model.score(X_train, y_train)
        test_acc = model.score(X_test, y_test)
        n_nonzero = np.sum(np.abs(model.coef_) > 0.01)
        
        print(f"{name:20s}: Train={train_acc:.3f}, Test={test_acc:.3f}, NonZero={n_nonzero}")
        results.append({
            'name': name,
            'coef': model.coef_.flatten(),
            'test_acc': test_acc
        })
    
    # Visualize coefficients
    fig, ax = plt.subplots(figsize=(14, 6))
    
    x_pos = np.arange(20)
    width = 0.2
    
    for i, r in enumerate(results):
        ax.bar(x_pos + i*width, np.abs(r['coef']), width, 
               label=f"{r['name']} (acc={r['test_acc']:.2f})", alpha=0.7)
    
    ax.set_xlabel('Feature Index')
    ax.set_ylabel('|Coefficient|')
    ax.set_title('Effect of Regularization on Coefficient Magnitude')
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    plt.tight_layout()
    plt.show()

logistic_regression_mle_map()
```

---

## FAQs

### When does MLE = MAP?

When the prior is uniform (uninformative):
$$P(\theta) = const \Rightarrow \argmax P(\theta|D) = \argmax P(D|\theta)$$

### Why is MAP more robust to overfitting?

The prior acts as a regularizer, penalizing extreme parameter values. With limited data, MLE can fit noise; MAP pulls parameters toward the prior.

### How do I choose λ (regularization strength)?

1. **Cross-validation**: Try different values, pick best validation performance
2. **Bayesian way**: Treat λ as hyperparameter with its own prior (empirical Bayes)
3. **Rule of thumb**: Start with λ = 1, adjust based on validation

### Can I get uncertainty with MLE?

MLE gives point estimates. For uncertainty:
- Use bootstrap for confidence intervals
- Or switch to full Bayesian (posterior distribution)
- Or use approximate methods like MC Dropout

---

## Key Takeaways

1. **MLE maximizes likelihood**: Best parameters that explain data
2. **MAP maximizes posterior**: Best parameters given data AND prior
3. **Regularization = Prior**: L2 ↔ Gaussian, L1 ↔ Laplace
4. **More data → MLE ≈ MAP**: Prior becomes less important
5. **Small data → use MAP**: Prior prevents overfitting
6. **Deep learning**: Weight decay is MAP with Gaussian prior

---

## Next Steps

Continue your statistical learning:

1. **[Bayes' Theorem](/docs/ai-ml/foundations/mathematics/probability/bayes-theorem-explained/)** - Foundation concepts
2. **[Statistical Inference](/docs/ai-ml/foundations/mathematics/probability/statistical-inference-ml/)** - Hypothesis testing
3. **[Information Theory](/docs/ai-ml/foundations/mathematics/probability/information-theory-entropy/)** - Entropy and KL divergence

---

## References

1. Bishop, C. M. "Pattern Recognition and Machine Learning" - Chapter 3
2. Murphy, K. P. "Machine Learning: A Probabilistic Perspective" - Chapters 5-6
3. Goodfellow, I. et al. "Deep Learning" - Chapter 5
4. Gal, Y., Ghahramani, Z. "Dropout as a Bayesian Approximation" (2016)

---

*Last updated: January 2024. Part of our [Mathematics for Machine Learning](/docs/ai-ml/foundations/mathematics/) series.*
