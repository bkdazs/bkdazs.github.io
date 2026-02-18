---
title: "Information Theory: Entropy, Cross-Entropy, and KL Divergence"
description: "Master information theory for machine learning. Learn entropy, cross-entropy loss, KL divergence, and mutual information with intuitive explanations and Python code."
date: 2024-01-15
lastmod: 2024-01-15
draft: false
weight: 6
toc: true
categories: ["AI/ML", "Mathematics", "Statistics"]
tags: ["entropy", "cross-entropy", "KL divergence", "information theory", "machine learning"]
series: ["Mathematics for ML"]
---

# Information Theory: Entropy, Cross-Entropy, and KL Divergence

**"Information theory is the mathematics of surprise. Machine learning is the art of reducing it."**

Information theory, developed by Claude Shannon, is fundamental to modern machine learning. From the cross-entropy loss function to variational autoencoders, these concepts appear everywhere. This guide makes them intuitive and practical.

---

## Why Information Theory in ML?

### Where It Appears

| ML Application | Information Theory Concept |
|----------------|---------------------------|
| Classification loss | Cross-entropy |
| VAE loss | KL divergence |
| Decision trees | Information gain (entropy) |
| Feature selection | Mutual information |
| Compression | Entropy coding |
| GANs | Jensen-Shannon divergence |
| Knowledge distillation | KL divergence |

```
Information Theory in the ML Pipeline:
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   Data ──► Features ──► Model ──► Predictions ──► Loss      │
│   │         │           │           │              │        │
│   │         │           │           │      Cross-Entropy    │
│   │         │      VAE: KL-Div      │                       │
│   │     Mutual Info                 │                       │
│   Entropy (compression)             │                       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Entropy: Measuring Uncertainty

### Shannon Entropy

For a discrete random variable X with probability distribution P:

$$H(X) = -\sum_{x} P(x) \log P(x) = E[-\log P(X)]$$

**Intuition**: Entropy measures the average "surprise" or uncertainty in a distribution.

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy import stats

def entropy_intuition():
    """Visualize entropy intuition."""
    
    fig, axes = plt.subplots(1, 3, figsize=(15, 5))
    
    distributions = [
        ([0.99, 0.01], "Low Entropy\n(Predictable)"),
        ([0.5, 0.5], "Max Entropy\n(Uncertain)"),
        ([0.25, 0.25, 0.25, 0.25], "Uniform (Max for 4 outcomes)"),
    ]
    
    def entropy(p):
        p = np.array(p)
        p = p[p > 0]  # Avoid log(0)
        return -np.sum(p * np.log2(p))
    
    for ax, (probs, title) in zip(axes, distributions):
        probs = np.array(probs)
        H = entropy(probs)
        
        ax.bar(range(len(probs)), probs, color='steelblue', alpha=0.7)
        ax.set_xlabel('Outcome')
        ax.set_ylabel('Probability')
        ax.set_title(f'{title}\nH = {H:.3f} bits')
        ax.set_ylim(0, 1.1)
        
        for i, p in enumerate(probs):
            ax.text(i, p + 0.02, f'{p:.2f}', ha='center')
    
    plt.tight_layout()
    plt.show()
    
    print("Entropy Intuition:")
    print("• Low entropy = Predictable (certain outcome)")
    print("• High entropy = Uncertain (all outcomes equally likely)")
    print("• Maximum entropy for n outcomes = log₂(n) bits")

entropy_intuition()
```

### Entropy of Binary Variable

For Bernoulli(p): $H(p) = -p \log p - (1-p) \log(1-p)$

```python
def binary_entropy():
    """Binary entropy function."""
    
    p = np.linspace(0.001, 0.999, 1000)
    H = -p * np.log2(p) - (1-p) * np.log2(1-p)
    
    fig, ax = plt.subplots(figsize=(10, 6))
    
    ax.plot(p, H, 'b-', linewidth=2)
    ax.axvline(0.5, color='red', linestyle='--', alpha=0.5, label='p=0.5 (max)')
    ax.axhline(1.0, color='gray', linestyle=':', alpha=0.5)
    
    # Mark special points
    special_p = [0.1, 0.3, 0.5, 0.7, 0.9]
    for sp in special_p:
        H_p = -sp * np.log2(sp) - (1-sp) * np.log2(1-sp)
        ax.scatter([sp], [H_p], s=100, zorder=5)
        ax.annotate(f'({sp}, {H_p:.2f})', (sp, H_p), 
                   textcoords="offset points", xytext=(10, 10))
    
    ax.set_xlabel('p (probability of success)')
    ax.set_ylabel('H(p) in bits')
    ax.set_title('Binary Entropy Function')
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    plt.tight_layout()
    plt.show()
    
    print("Binary Entropy:")
    print("• H(0) = H(1) = 0 bits (certain outcome)")
    print("• H(0.5) = 1 bit (maximum uncertainty)")
    print("• Symmetric around p = 0.5")

binary_entropy()
```

### Information Content

The information content (self-information) of an event with probability p:

$$I(p) = -\log p$$

**Intuition**: Rare events carry more information!

```python
def information_content():
    """Information content of events."""
    
    events = [
        ("Sun rises tomorrow", 0.9999, "Expected"),
        ("Rain in Seattle", 0.5, "Uncertain"),
        ("Win lottery", 0.0000001, "Rare"),
    ]
    
    print("Information Content of Events")
    print("=" * 60)
    
    for event, prob, category in events:
        info = -np.log2(prob)
        print(f"{event:25s}: P = {prob:.7f}, Info = {info:.2f} bits ({category})")
    
    print("\nKey insight: Rare events are more 'informative' (surprising)!")
    
    # Visualize
    fig, ax = plt.subplots(figsize=(10, 5))
    
    p = np.linspace(0.01, 1, 100)
    info = -np.log2(p)
    
    ax.plot(p, info, 'b-', linewidth=2)
    ax.set_xlabel('Probability p')
    ax.set_ylabel('Information I(p) = -log₂(p) bits')
    ax.set_title('Information Content: Rare Events Carry More Information')
    ax.grid(True, alpha=0.3)
    
    plt.tight_layout()
    plt.show()

information_content()
```

---

## Cross-Entropy: The Loss Function

### Definition

Cross-entropy between true distribution P and predicted distribution Q:

$$H(P, Q) = -\sum_{x} P(x) \log Q(x) = E_P[-\log Q(X)]$$

**ML interpretation**: Average bits needed to encode data from P using code optimized for Q.

```python
def cross_entropy_explained():
    """Cross-entropy as a loss function."""
    
    # True distribution (one-hot for classification)
    y_true = np.array([1, 0, 0, 0])  # Class 0 is correct
    
    # Different predictions
    predictions = [
        ("Perfect", [1.0, 0.0, 0.0, 0.0]),
        ("Good", [0.8, 0.1, 0.05, 0.05]),
        ("Uncertain", [0.4, 0.3, 0.2, 0.1]),
        ("Wrong", [0.1, 0.6, 0.2, 0.1]),
        ("Very wrong", [0.01, 0.01, 0.01, 0.97]),
    ]
    
    def cross_entropy(p_true, q_pred):
        """Compute cross-entropy."""
        q_pred = np.clip(q_pred, 1e-10, 1.0)  # Avoid log(0)
        return -np.sum(p_true * np.log(q_pred))
    
    print("Cross-Entropy Loss for Classification")
    print("=" * 60)
    print(f"True label: Class 0 (one-hot: {list(y_true)})")
    print()
    
    losses = []
    for name, pred in predictions:
        pred = np.array(pred)
        loss = cross_entropy(y_true, pred)
        losses.append(loss)
        print(f"{name:15s}: {list(pred)} → Loss = {loss:.4f}")
    
    # Visualize
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    
    ax = axes[0]
    x = np.arange(4)
    width = 0.15
    
    for i, (name, pred) in enumerate(predictions):
        ax.bar(x + i*width, pred, width, label=name, alpha=0.7)
    
    ax.set_xlabel('Class')
    ax.set_ylabel('Predicted Probability')
    ax.set_title('Different Predictions')
    ax.set_xticks(x + 2*width)
    ax.set_xticklabels(['Class 0 (True)', 'Class 1', 'Class 2', 'Class 3'])
    ax.legend()
    
    ax = axes[1]
    ax.bar([name for name, _ in predictions], losses, color='steelblue', alpha=0.7)
    ax.set_xlabel('Prediction Quality')
    ax.set_ylabel('Cross-Entropy Loss')
    ax.set_title('Loss Increases with Worse Predictions')
    ax.tick_params(axis='x', rotation=45)
    
    plt.tight_layout()
    plt.show()

cross_entropy_explained()
```

### Binary Cross-Entropy

For binary classification:

$$H(y, \hat{y}) = -[y \log(\hat{y}) + (1-y) \log(1-\hat{y})]$$

```python
def binary_cross_entropy():
    """Binary cross-entropy loss."""
    
    y_true = 1  # Positive example
    
    # Range of predictions
    y_pred = np.linspace(0.001, 0.999, 1000)
    
    # BCE for y=1
    bce_pos = -np.log(y_pred)
    
    # BCE for y=0
    bce_neg = -np.log(1 - y_pred)
    
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    
    ax = axes[0]
    ax.plot(y_pred, bce_pos, 'b-', linewidth=2, label='True y = 1')
    ax.plot(y_pred, bce_neg, 'r-', linewidth=2, label='True y = 0')
    ax.set_xlabel('Predicted Probability ŷ')
    ax.set_ylabel('BCE Loss')
    ax.set_title('Binary Cross-Entropy Loss')
    ax.legend()
    ax.set_ylim(0, 5)
    ax.grid(True, alpha=0.3)
    
    # Connection to log loss
    ax = axes[1]
    
    # Average BCE over dataset
    y_true_batch = np.array([1, 1, 0, 0, 1])
    y_pred_batch = np.array([0.9, 0.7, 0.3, 0.1, 0.8])
    
    individual_losses = -(y_true_batch * np.log(y_pred_batch) + 
                          (1 - y_true_batch) * np.log(1 - y_pred_batch))
    
    ax.bar(range(len(y_true_batch)), individual_losses, alpha=0.7)
    for i, (yt, yp, loss) in enumerate(zip(y_true_batch, y_pred_batch, individual_losses)):
        ax.text(i, loss + 0.05, f'y={yt}\nŷ={yp}', ha='center', fontsize=9)
    
    ax.axhline(individual_losses.mean(), color='red', linestyle='--',
               label=f'Mean BCE = {individual_losses.mean():.3f}')
    ax.set_xlabel('Sample')
    ax.set_ylabel('BCE Loss')
    ax.set_title('BCE for Individual Samples')
    ax.legend()
    
    plt.tight_layout()
    plt.show()
    
    print("Binary Cross-Entropy:")
    print("• When y=1: Loss = -log(ŷ), penalizes ŷ close to 0")
    print("• When y=0: Loss = -log(1-ŷ), penalizes ŷ close to 1")

binary_cross_entropy()
```

### Why Cross-Entropy for Classification?

```python
def why_cross_entropy():
    """Why cross-entropy is better than MSE for classification."""
    
    y_true = 1
    y_pred = np.linspace(0.001, 0.999, 1000)
    
    # MSE loss
    mse = (y_true - y_pred) ** 2
    
    # Cross-entropy loss
    ce = -np.log(y_pred)
    
    # Gradients
    grad_mse = 2 * (y_pred - y_true)
    grad_ce = -1 / y_pred
    
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    
    ax = axes[0]
    ax.plot(y_pred, mse, 'b-', linewidth=2, label='MSE')
    ax.plot(y_pred, ce, 'r-', linewidth=2, label='Cross-Entropy')
    ax.set_xlabel('Predicted Probability ŷ (True y = 1)')
    ax.set_ylabel('Loss')
    ax.set_title('Loss Functions Comparison')
    ax.legend()
    ax.set_ylim(0, 5)
    ax.grid(True, alpha=0.3)
    
    ax = axes[1]
    ax.plot(y_pred, np.abs(grad_mse), 'b-', linewidth=2, label='|∇MSE|')
    ax.plot(y_pred, np.abs(grad_ce), 'r-', linewidth=2, label='|∇CE|')
    ax.set_xlabel('Predicted Probability ŷ (True y = 1)')
    ax.set_ylabel('|Gradient|')
    ax.set_title('Gradient Magnitude')
    ax.legend()
    ax.set_ylim(0, 10)
    ax.grid(True, alpha=0.3)
    
    plt.tight_layout()
    plt.show()
    
    print("Why Cross-Entropy > MSE for Classification:")
    print("• MSE gradient vanishes when prediction is very wrong (ŷ ≈ 0)")
    print("• CE gradient is large when wrong → faster learning")
    print("• CE comes from MLE of Bernoulli distribution")

why_cross_entropy()
```

---

## KL Divergence: Measuring Distribution Difference

### Definition

Kullback-Leibler divergence from Q to P:

$$D_{KL}(P \| Q) = \sum_{x} P(x) \log \frac{P(x)}{Q(x)} = E_P\left[\log \frac{P(X)}{Q(X)}\right]$$

**Properties**:
- $D_{KL}(P \| Q) \geq 0$ (Gibbs inequality)
- $D_{KL}(P \| Q) = 0$ iff $P = Q$
- **Not symmetric**: $D_{KL}(P \| Q) \neq D_{KL}(Q \| P)$

```python
def kl_divergence_basics():
    """KL divergence intuition and properties."""
    
    # Two distributions
    P = np.array([0.4, 0.3, 0.2, 0.1])  # True distribution
    Q = np.array([0.25, 0.25, 0.25, 0.25])  # Approximation (uniform)
    
    def kl_divergence(P, Q):
        """Compute KL divergence."""
        P = np.array(P)
        Q = np.array(Q)
        # Only sum where P > 0
        mask = P > 0
        return np.sum(P[mask] * np.log(P[mask] / Q[mask]))
    
    kl_pq = kl_divergence(P, Q)
    kl_qp = kl_divergence(Q, P)
    
    print("KL Divergence")
    print("=" * 50)
    print(f"P (true):  {list(P)}")
    print(f"Q (approx): {list(Q)}")
    print(f"\nD_KL(P || Q) = {kl_pq:.4f} nats")
    print(f"D_KL(Q || P) = {kl_qp:.4f} nats")
    print(f"\nAsymmetric! D_KL(P||Q) ≠ D_KL(Q||P)")
    
    # Relationship to cross-entropy
    H_P = -np.sum(P * np.log(P))  # Entropy of P
    H_PQ = -np.sum(P * np.log(Q))  # Cross-entropy
    
    print(f"\nRelationship:")
    print(f"H(P) = {H_P:.4f} (entropy)")
    print(f"H(P,Q) = {H_PQ:.4f} (cross-entropy)")
    print(f"D_KL(P||Q) = H(P,Q) - H(P) = {H_PQ - H_P:.4f}")
    print(f"\n→ Cross-entropy = Entropy + KL Divergence")
    
    # Visualize
    fig, ax = plt.subplots(figsize=(10, 6))
    
    x = np.arange(4)
    width = 0.35
    
    ax.bar(x - width/2, P, width, label='P (true)', alpha=0.7)
    ax.bar(x + width/2, Q, width, label='Q (approx)', alpha=0.7)
    
    ax.set_xlabel('Outcome')
    ax.set_ylabel('Probability')
    ax.set_title(f'KL Divergence: D_KL(P||Q) = {kl_pq:.4f}')
    ax.set_xticks(x)
    ax.legend()
    
    plt.tight_layout()
    plt.show()

kl_divergence_basics()
```

### KL Divergence Asymmetry

```python
def kl_asymmetry():
    """Visualize KL divergence asymmetry."""
    
    def kl_div(P, Q):
        mask = P > 0
        return np.sum(P[mask] * np.log(P[mask] / Q[mask]))
    
    # Create grid of distributions
    fig, axes = plt.subplots(2, 2, figsize=(12, 10))
    
    # P is narrow, Q is wide
    x = np.linspace(-5, 5, 100)
    P1 = stats.norm.pdf(x, 0, 0.5)
    Q1 = stats.norm.pdf(x, 0, 2)
    
    # P is wide, Q is narrow
    P2 = stats.norm.pdf(x, 0, 2)
    Q2 = stats.norm.pdf(x, 0, 0.5)
    
    # Normalize
    P1, Q1 = P1/P1.sum(), Q1/Q1.sum()
    P2, Q2 = P2/P2.sum(), Q2/Q2.sum()
    
    scenarios = [
        (P1, Q1, "P narrow, Q wide", axes[0, 0]),
        (Q1, P1, "P wide, Q narrow", axes[0, 1]),
    ]
    
    ax = axes[0, 0]
    ax.plot(x, P1, 'b-', linewidth=2, label='P (true)')
    ax.plot(x, Q1, 'r--', linewidth=2, label='Q (model)')
    kl = kl_div(P1, Q1)
    ax.set_title(f'D_KL(P||Q) = {kl:.3f}\nP narrow, Q wide')
    ax.legend()
    ax.set_xlabel('x')
    
    ax = axes[0, 1]
    ax.plot(x, Q1, 'b-', linewidth=2, label='P (true)')
    ax.plot(x, P1, 'r--', linewidth=2, label='Q (model)')
    kl = kl_div(Q1, P1)
    ax.set_title(f'D_KL(P||Q) = {kl:.3f}\nP wide, Q narrow')
    ax.legend()
    ax.set_xlabel('x')
    
    # Behavior explanation
    ax = axes[1, 0]
    ax.text(0.5, 0.7, "D_KL(P||Q) = Forward KL", fontsize=14, ha='center', 
            transform=ax.transAxes, fontweight='bold')
    ax.text(0.5, 0.5, "• Minimizing: Q covers where P is high", fontsize=11, 
            ha='center', transform=ax.transAxes)
    ax.text(0.5, 0.35, "• Mode-covering behavior", fontsize=11, 
            ha='center', transform=ax.transAxes)
    ax.text(0.5, 0.2, "• Used in variational inference", fontsize=11, 
            ha='center', transform=ax.transAxes)
    ax.axis('off')
    
    ax = axes[1, 1]
    ax.text(0.5, 0.7, "D_KL(Q||P) = Reverse KL", fontsize=14, ha='center', 
            transform=ax.transAxes, fontweight='bold')
    ax.text(0.5, 0.5, "• Minimizing: Q avoids where P is low", fontsize=11, 
            ha='center', transform=ax.transAxes)
    ax.text(0.5, 0.35, "• Mode-seeking behavior", fontsize=11, 
            ha='center', transform=ax.transAxes)
    ax.text(0.5, 0.2, "• Used in VAE (ELBO)", fontsize=11, 
            ha='center', transform=ax.transAxes)
    ax.axis('off')
    
    plt.tight_layout()
    plt.show()

kl_asymmetry()
```

### KL Divergence in VAEs

```python
def kl_in_vae():
    """KL divergence in Variational Autoencoders."""
    
    print("KL Divergence in VAEs")
    print("=" * 60)
    
    print("""
    VAE Loss = Reconstruction Loss + β × KL Divergence
    
    KL term: D_KL(q(z|x) || p(z))
    
    Where:
    • q(z|x) = encoder output (approximate posterior)
    • p(z) = prior (usually N(0, I))
    
    For Gaussian q(z|x) = N(μ, σ²) and p(z) = N(0, 1):
    
    D_KL = -½ Σ (1 + log(σ²) - μ² - σ²)
    """)
    
    def kl_gaussian(mu, log_var):
        """KL divergence between N(mu, exp(log_var)) and N(0, 1)."""
        return -0.5 * np.sum(1 + log_var - mu**2 - np.exp(log_var))
    
    # Examples
    examples = [
        ((0, 0), "Perfect match (μ=0, σ=1)"),
        ((1, 0), "Mean shifted (μ=1, σ=1)"),
        ((0, np.log(2)), "Variance doubled (μ=0, σ=√2)"),
        ((2, np.log(0.5)), "Mean and var different"),
    ]
    
    print("\nExamples (1D):")
    for (mu, log_var), desc in examples:
        kl = kl_gaussian(np.array([mu]), np.array([log_var]))
        print(f"  {desc:35s}: KL = {kl:.4f}")
    
    # Visualize
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    
    x = np.linspace(-4, 4, 100)
    prior = stats.norm.pdf(x, 0, 1)
    
    ax = axes[0]
    ax.plot(x, prior, 'k--', linewidth=2, label='Prior N(0,1)')
    
    for (mu, log_var), desc in examples[:3]:
        sigma = np.sqrt(np.exp(log_var))
        posterior = stats.norm.pdf(x, mu, sigma)
        kl = kl_gaussian(np.array([mu]), np.array([log_var]))
        ax.plot(x, posterior, linewidth=2, label=f'{desc[:20]} (KL={kl:.2f})')
    
    ax.set_xlabel('z')
    ax.set_ylabel('Density')
    ax.set_title('VAE: Encoder Distribution vs Prior')
    ax.legend()
    
    # KL as function of mu and sigma
    ax = axes[1]
    
    mu_range = np.linspace(-2, 2, 100)
    sigma_range = np.linspace(0.1, 3, 100)
    
    MU, SIGMA = np.meshgrid(mu_range, sigma_range)
    KL = 0.5 * (MU**2 + SIGMA**2 - np.log(SIGMA**2) - 1)
    
    contour = ax.contourf(MU, SIGMA, KL, levels=20, cmap='viridis')
    ax.scatter([0], [1], s=200, c='red', marker='*', label='Minimum (μ=0, σ=1)')
    plt.colorbar(contour, ax=ax, label='KL Divergence')
    ax.set_xlabel('μ')
    ax.set_ylabel('σ')
    ax.set_title('KL Divergence Surface')
    ax.legend()
    
    plt.tight_layout()
    plt.show()

kl_in_vae()
```

---

## Mutual Information

### Definition

Mutual information measures how much knowing X tells us about Y:

$$I(X; Y) = D_{KL}(P_{X,Y} \| P_X P_Y) = H(X) - H(X|Y) = H(Y) - H(Y|X)$$

```python
def mutual_information_example():
    """Mutual information between variables."""
    
    # Joint distribution
    # X: Weather (sunny=0, rainy=1)
    # Y: Umbrella (no=0, yes=1)
    
    P_XY = np.array([
        [0.4, 0.1],   # Sunny: P(no umbrella), P(umbrella)
        [0.05, 0.45]  # Rainy: P(no umbrella), P(umbrella)
    ])
    
    # Marginals
    P_X = P_XY.sum(axis=1)  # P(weather)
    P_Y = P_XY.sum(axis=0)  # P(umbrella)
    
    # Entropy
    def entropy(p):
        p = p[p > 0]
        return -np.sum(p * np.log2(p))
    
    H_X = entropy(P_X)
    H_Y = entropy(P_Y)
    H_XY = entropy(P_XY.flatten())
    
    # Mutual information
    I_XY = H_X + H_Y - H_XY
    
    # Alternative: KL between joint and product of marginals
    P_X_P_Y = np.outer(P_X, P_Y)
    I_XY_kl = np.sum(P_XY * np.log2(P_XY / P_X_P_Y + 1e-10))
    
    print("Mutual Information Example")
    print("=" * 60)
    print("Joint Distribution P(Weather, Umbrella):")
    print(f"                  No Umbrella  Umbrella")
    print(f"  Sunny           {P_XY[0,0]:.2f}         {P_XY[0,1]:.2f}")
    print(f"  Rainy           {P_XY[1,0]:.2f}         {P_XY[1,1]:.2f}")
    
    print(f"\nMarginals:")
    print(f"  P(Sunny) = {P_X[0]:.2f}, P(Rainy) = {P_X[1]:.2f}")
    print(f"  P(No Umbrella) = {P_Y[0]:.2f}, P(Umbrella) = {P_Y[1]:.2f}")
    
    print(f"\nEntropies:")
    print(f"  H(Weather) = {H_X:.3f} bits")
    print(f"  H(Umbrella) = {H_Y:.3f} bits")
    print(f"  H(Weather, Umbrella) = {H_XY:.3f} bits")
    
    print(f"\nMutual Information:")
    print(f"  I(Weather; Umbrella) = {I_XY:.3f} bits")
    print(f"  = H(X) + H(Y) - H(X,Y)")
    print(f"  = {H_X:.3f} + {H_Y:.3f} - {H_XY:.3f}")
    
    print(f"\nInterpretation: Knowing umbrella status tells us {I_XY:.3f} bits about weather")
    
    # Visualize
    fig, axes = plt.subplots(1, 3, figsize=(15, 5))
    
    # Joint distribution
    ax = axes[0]
    im = ax.imshow(P_XY, cmap='Blues')
    ax.set_xticks([0, 1])
    ax.set_yticks([0, 1])
    ax.set_xticklabels(['No Umbrella', 'Umbrella'])
    ax.set_yticklabels(['Sunny', 'Rainy'])
    ax.set_title('Joint Distribution P(X,Y)')
    for i in range(2):
        for j in range(2):
            ax.text(j, i, f'{P_XY[i,j]:.2f}', ha='center', va='center')
    plt.colorbar(im, ax=ax)
    
    # If independent
    ax = axes[1]
    im = ax.imshow(P_X_P_Y, cmap='Blues')
    ax.set_xticks([0, 1])
    ax.set_yticks([0, 1])
    ax.set_xticklabels(['No Umbrella', 'Umbrella'])
    ax.set_yticklabels(['Sunny', 'Rainy'])
    ax.set_title('If Independent: P(X)P(Y)')
    for i in range(2):
        for j in range(2):
            ax.text(j, i, f'{P_X_P_Y[i,j]:.2f}', ha='center', va='center')
    plt.colorbar(im, ax=ax)
    
    # Venn diagram style
    ax = axes[2]
    from matplotlib.patches import Circle
    
    c1 = Circle((0.35, 0.5), 0.3, alpha=0.5, color='blue', label=f'H(X)={H_X:.2f}')
    c2 = Circle((0.65, 0.5), 0.3, alpha=0.5, color='red', label=f'H(Y)={H_Y:.2f}')
    
    ax.add_patch(c1)
    ax.add_patch(c2)
    ax.text(0.5, 0.5, f'I={I_XY:.2f}', ha='center', va='center', fontsize=12)
    ax.set_xlim(0, 1)
    ax.set_ylim(0, 1)
    ax.set_aspect('equal')
    ax.legend()
    ax.set_title('Information Diagram')
    ax.axis('off')
    
    plt.tight_layout()
    plt.show()

mutual_information_example()
```

### Mutual Information for Feature Selection

```python
def mutual_information_feature_selection():
    """Use mutual information for feature selection."""
    
    from sklearn.feature_selection import mutual_info_classif
    from sklearn.datasets import load_iris
    
    # Load data
    iris = load_iris()
    X = iris.data
    y = iris.target
    
    # Compute mutual information
    mi_scores = mutual_info_classif(X, y, random_state=42)
    
    print("Mutual Information for Feature Selection")
    print("=" * 60)
    
    for name, score in sorted(zip(iris.feature_names, mi_scores), 
                              key=lambda x: -x[1]):
        print(f"  {name:20s}: MI = {score:.4f}")
    
    # Visualize
    fig, ax = plt.subplots(figsize=(10, 5))
    
    ax.barh(iris.feature_names, mi_scores, color='steelblue', alpha=0.7)
    ax.set_xlabel('Mutual Information (nats)')
    ax.set_title('Feature Importance via Mutual Information')
    ax.grid(True, alpha=0.3)
    
    plt.tight_layout()
    plt.show()
    
    print("\nAdvantage: MI captures non-linear relationships!")

mutual_information_feature_selection()
```

---

## Entropy in Decision Trees

### Information Gain

$$\text{Information Gain} = H(Y) - H(Y | X)$$

```python
def decision_tree_entropy():
    """Entropy and information gain in decision trees."""
    
    # Example: Should we play tennis?
    # Features: Outlook, Temperature, Humidity, Wind
    # Target: Play (yes/no)
    
    # Simplified example
    # Before split: 9 yes, 5 no
    total_yes, total_no = 9, 5
    total = total_yes + total_no
    
    def entropy(yes, no):
        total = yes + no
        if total == 0:
            return 0
        p_yes = yes / total
        p_no = no / total
        if p_yes == 0 or p_no == 0:
            return 0
        return -p_yes * np.log2(p_yes) - p_no * np.log2(p_no)
    
    H_before = entropy(total_yes, total_no)
    
    # After split on Outlook
    # Sunny: 2 yes, 3 no
    # Overcast: 4 yes, 0 no
    # Rainy: 3 yes, 2 no
    
    splits = {
        'Sunny': (2, 3),
        'Overcast': (4, 0),
        'Rainy': (3, 2)
    }
    
    # Weighted entropy after split
    H_after = 0
    for outcome, (yes, no) in splits.items():
        weight = (yes + no) / total
        H_after += weight * entropy(yes, no)
    
    info_gain = H_before - H_after
    
    print("Decision Tree: Information Gain")
    print("=" * 60)
    print(f"Before split: {total_yes} yes, {total_no} no")
    print(f"H(Play) = {H_before:.4f} bits")
    
    print(f"\nAfter split on 'Outlook':")
    for outcome, (yes, no) in splits.items():
        H = entropy(yes, no)
        print(f"  {outcome:10s}: {yes} yes, {no} no → H = {H:.4f}")
    
    print(f"\nWeighted entropy: {H_after:.4f}")
    print(f"Information Gain = {H_before:.4f} - {H_after:.4f} = {info_gain:.4f}")
    
    # Compare with other splits
    print("\n" + "=" * 60)
    print("Comparing Different Split Features:")
    
    other_splits = {
        'Humidity': {'High': (3, 4), 'Normal': (6, 1)},
        'Wind': {'Strong': (3, 3), 'Weak': (6, 2)},
    }
    
    for feature, outcomes in other_splits.items():
        H_after = 0
        for (yes, no) in outcomes.values():
            weight = (yes + no) / total
            H_after += weight * entropy(yes, no)
        ig = H_before - H_after
        print(f"  {feature:10s}: IG = {ig:.4f}")
    
    print(f"  Outlook   : IG = {info_gain:.4f} (best!)")

decision_tree_entropy()
```

---

## Other Divergences

### Jensen-Shannon Divergence

Symmetric version of KL divergence:

$$D_{JS}(P \| Q) = \frac{1}{2} D_{KL}(P \| M) + \frac{1}{2} D_{KL}(Q \| M)$$

where $M = \frac{1}{2}(P + Q)$

```python
def jensen_shannon():
    """Jensen-Shannon divergence."""
    
    def kl_div(P, Q):
        mask = P > 0
        return np.sum(P[mask] * np.log(P[mask] / Q[mask]))
    
    def js_div(P, Q):
        M = 0.5 * (P + Q)
        return 0.5 * kl_div(P, M) + 0.5 * kl_div(Q, M)
    
    # Example distributions
    P = np.array([0.5, 0.3, 0.2])
    Q = np.array([0.2, 0.4, 0.4])
    
    print("Jensen-Shannon vs KL Divergence")
    print("=" * 50)
    print(f"P = {list(P)}")
    print(f"Q = {list(Q)}")
    
    print(f"\nKL Divergence:")
    print(f"  D_KL(P||Q) = {kl_div(P, Q):.4f}")
    print(f"  D_KL(Q||P) = {kl_div(Q, P):.4f}")
    print(f"  (Asymmetric!)")
    
    print(f"\nJensen-Shannon Divergence:")
    print(f"  D_JS(P||Q) = {js_div(P, Q):.4f}")
    print(f"  D_JS(Q||P) = {js_div(Q, P):.4f}")
    print(f"  (Symmetric!)")
    
    print("\nJS divergence is used in GANs (original formulation)")

jensen_shannon()
```

### f-Divergences Family

```python
def f_divergences():
    """Overview of f-divergences."""
    
    print("f-Divergences Family")
    print("=" * 60)
    
    divergences = [
        ("KL Divergence", "D_KL(P||Q)", "f(t) = t log(t)", "VAE, cross-entropy"),
        ("Reverse KL", "D_KL(Q||P)", "f(t) = -log(t)", "Policy optimization"),
        ("Jensen-Shannon", "D_JS(P||Q)", "f(t) = -(t+1)log((t+1)/2) + t log(t)", "Original GAN"),
        ("Total Variation", "TV(P||Q)", "f(t) = |t - 1| / 2", "Robust estimation"),
        ("Chi-squared", "χ²(P||Q)", "f(t) = (t - 1)²", "Goodness of fit"),
        ("Hellinger", "H²(P||Q)", "f(t) = (√t - 1)²", "Robust statistics"),
    ]
    
    print(f"{'Name':20s} {'Formula':15s} {'Used in':25s}")
    print("-" * 60)
    for name, formula, _, use in divergences:
        print(f"{name:20s} {formula:15s} {use:25s}")

f_divergences()
```

---

## Practical Applications

### Label Smoothing

```python
def label_smoothing():
    """Label smoothing reduces cross-entropy overconfidence."""
    
    n_classes = 5
    true_class = 2
    smoothing = 0.1
    
    # Hard labels
    hard_label = np.zeros(n_classes)
    hard_label[true_class] = 1.0
    
    # Soft labels
    soft_label = np.ones(n_classes) * smoothing / n_classes
    soft_label[true_class] = 1.0 - smoothing + smoothing / n_classes
    
    print("Label Smoothing")
    print("=" * 50)
    print(f"True class: {true_class}")
    print(f"Smoothing: {smoothing}")
    print(f"\nHard labels: {hard_label.round(3)}")
    print(f"Soft labels: {soft_label.round(3)}")
    
    # Effect on loss
    confident_pred = np.array([0.01, 0.01, 0.95, 0.02, 0.01])
    
    def cross_entropy(p_true, p_pred):
        return -np.sum(p_true * np.log(p_pred + 1e-10))
    
    loss_hard = cross_entropy(hard_label, confident_pred)
    loss_soft = cross_entropy(soft_label, confident_pred)
    
    print(f"\nFor confident prediction {confident_pred.round(2)}:")
    print(f"  Loss with hard labels: {loss_hard:.4f}")
    print(f"  Loss with soft labels: {loss_soft:.4f}")
    print(f"\nSoft labels penalize overconfidence!")

label_smoothing()
```

### Temperature Scaling (Knowledge Distillation)

```python
def knowledge_distillation():
    """Temperature in knowledge distillation."""
    
    # Teacher logits
    logits = np.array([2.0, 1.0, 0.5, 0.1, -0.5])
    
    def softmax(x, T=1.0):
        exp_x = np.exp(x / T)
        return exp_x / exp_x.sum()
    
    print("Knowledge Distillation: Temperature")
    print("=" * 60)
    
    temperatures = [0.5, 1.0, 2.0, 5.0, 10.0]
    
    fig, ax = plt.subplots(figsize=(12, 6))
    
    x = np.arange(5)
    width = 0.15
    
    for i, T in enumerate(temperatures):
        probs = softmax(logits, T)
        H = -np.sum(probs * np.log(probs))
        ax.bar(x + i*width, probs, width, label=f'T={T} (H={H:.2f})', alpha=0.7)
        
        print(f"T={T:4.1f}: {probs.round(3)} (Entropy={H:.3f})")
    
    ax.set_xlabel('Class')
    ax.set_ylabel('Probability')
    ax.set_title('Softmax with Different Temperatures')
    ax.set_xticks(x + 2*width)
    ax.legend()
    
    plt.tight_layout()
    plt.show()
    
    print("\nHigher T → softer distribution → more knowledge transfer")

knowledge_distillation()
```

---

## FAQs

### Why is cross-entropy used instead of KL divergence for classification?

They're related! For classification with one-hot labels:
$$D_{KL}(P \| Q) = H(P, Q) - H(P)$$

Since $H(P) = 0$ for one-hot labels, minimizing cross-entropy = minimizing KL divergence.

### What's the unit of entropy?

- **bits** when using log₂
- **nats** when using ln (natural log)
- **bans** when using log₁₀

### Why is KL divergence asymmetric?

KL(P||Q) measures how well Q approximates P. It penalizes Q(x)≈0 where P(x)>0 infinitely, but not vice versa.

---

## Key Takeaways

1. **Entropy** measures uncertainty in a distribution
2. **Cross-entropy** is the loss function for classification
3. **KL divergence** measures distribution difference (asymmetric)
4. **Mutual information** captures dependencies between variables
5. **Information gain** guides decision tree splits
6. **Label smoothing** prevents overconfidence using soft targets

---

## Next Steps

Continue your ML mathematics journey:

1. **[Probability Distributions](/docs/ai-ml/foundations/mathematics/probability/probability-distributions-machine-learning/)** - Foundation concepts
2. **[Bayes' Theorem](/docs/ai-ml/foundations/mathematics/probability/bayes-theorem-explained/)** - Bayesian inference
3. **[Loss Functions](/docs/ai-ml/fundamentals/supervised/loss-functions/)** - Cross-entropy in practice

---

## References

1. Cover, T. M., Thomas, J. A. "Elements of Information Theory" (2nd ed.)
2. MacKay, D. J. C. "Information Theory, Inference, and Learning Algorithms"
3. Bishop, C. M. "Pattern Recognition and Machine Learning" - Chapter 1.6
4. Goodfellow, I. et al. "Deep Learning" - Chapter 3

---

*Last updated: January 2024. Part of our [Mathematics for Machine Learning](/docs/ai-ml/foundations/mathematics/) series.*
